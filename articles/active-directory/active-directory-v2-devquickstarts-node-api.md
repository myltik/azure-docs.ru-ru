<properties
	pageTitle="Веб-API Node.js для Azure AD версии 2.0 | Microsoft Azure"
	description="Как создать веб-API Node JS, принимающий маркеры доступа личных учетных записей Майкрософт, а также рабочих и учебных учетных записей."
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

# Защита веб-API с помощью Node.js

> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).

Используя конечную точку Azure Active Directory версии 2.0, вы можете защитить веб-API с помощью маркеров доступа [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) и позволить пользователям входить в систему с помощью личной, рабочей или учебной учетной записи Майкрософт для безопасного доступа к веб-API.

**Passport** — промежуточный слой проверки подлинности для Node.js. Он имеет очень гибкую и модульную структуру, которая позволяет сравнительно незаметно размещать данный слой в любом приложении на основе Express или в веб-приложении Resitify. Полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Microsoft Azure Active Directory. Мы установим этот модуль, а затем добавим подключаемый модуль Microsoft Azure Active Directory `passport-azure-ad`.

## Загрузить
Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Готовое приложение также приводится в конце этого руководства.


## 1\. регистрация приложения;
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com) или выполните [эти подробные указания](active-directory-v2-app-registration.md). Не забудьте:

- запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.
- Добавьте для приложения **мобильную** платформу.
- Скопируйте с портала **универсальный код ресурса (URI) перенаправления**. Необходимо использовать стандартное значение `urn:ietf:wg:oauth:2.0:oob`.


## Шаг 2. Скачивание node.js для платформы
Чтобы успешно использовать этот пример, необходимо иметь рабочую установку Node.js.

Установите Node.js с сайта [http://nodejs.org](http://nodejs.org).

## Шаг 3. Установка MongoDB на платформе

Чтобы успешно использовать этот пример, необходимо иметь рабочую установку MongoDB. Мы будем использовать MongoDB, чтобы сделать наш интерфейс REST API постоянным для экземпляров сервера.

Установите MongoDB с сайта [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] В данном руководстве предполагается, что вы используете стандартный установочный пакет и стандартные конечные точки сервера для MongoDB, которыми на момент написания этой статьи являются mongodb://localhost.

## Шаг 4. Установка модулей Restify для веб-API

Мы будем использовать Resitfy для построения нашего интерфейса REST API. Restify представляет собой минимальную и гибкую платформу приложений Node.js, полученную на основе Express, которая имеет широкий набор возможностей, позволяющих создавать интерфейсы REST API поверх Connect.

### Установка Restify

В командной строке перейдите в каталог azuread. Если каталог **azuread** не существует, создайте его.

`cd azuread` или `mkdir azuread;`

Введите следующую команду:

`npm install restify`

Эта команда устанавливает Restify.

#### Вы получили сообщение об ошибке?

При использовании npm в некоторых операционных системах может появиться сообщение об ошибке Error: EPERM, chmod '/usr/local/bin/..' и предложение попробовать использовать учетную запись с правами администратора. В этом случае необходимо с помощью команды sudo запустить npm с более высоким уровнем привилегий.

#### Вы получили сообщение об ошибке, которая относится к DTrace?

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
	└── bunyan@0.22.0(mv@0.0.5)


## Шаг 5. Установка Passport.js в веб-API

[Passport](http://passportjs.org/) — промежуточный слой проверки подлинности для Node.js. Он имеет очень гибкую и модульную структуру, которая позволяет сравнительно незаметно размещать данный слой в любом приложении на основе Express или в веб-приложении Resitify. Полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Azure Active Directory. Мы установим этот модуль, а затем добавим подключаемый модуль стратегии Azure Active Directory.

В командной строке перейдите в каталог azuread.

Введите следующую команду для установки passport.js

`npm install passport`

Результат этой команды должен выглядеть примерно следующим образом:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Шаг 6. Добавление Passport-Azure-AD в веб-API

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
``

## Шаг 7. Добавление модулей MongoDB в веб-интерфейс API

Мы будем использовать MongoDB в качестве хранилища данных. По этой причине нам нужно установить широко используемый подключаемый модуль для управления моделями и схемами, которые называются Mongoose, а также драйвер базы данных для MongoDB, также называемый MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## Шаг 8. Установка дополнительных модулей

Далее мы установим остальные необходимые модули.


В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`


Введите следующие команды для установки следующих модулей в каталоге node\_modules:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## Шаг 9. Создание server.js с зависимостями

Файл server.js будет выполнять основную часть нашего функционала для нашего сервера веб-интерфейса API. Мы добавим в этот файл основную часть нашего кода. В производственных целях рекомендуется разделить функционал на небольшие файлы, такие как отдельные маршруты и контроллеры. В этой демонстрации для данного функционала мы будем использовать server.js.

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Создайте файл `server.js` в любом удобном редакторе и добавьте следующие сведения:

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

Сохраните файл. Мы вернемся к нему позже.

## Шаг 10. Создание файла конфигурации для сохранения параметров Azure AD

Данный файл кода передает параметры конфигурации из вашего портала Azure Active Directory в Passport.js. Эти значения конфигурации были созданы при добавлении веб-интерфейса API на портал в первой части настоящего пошагового руководства. Мы объясним, что нужно будет сделать со значениями этих параметров после копирования кода.


В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Создайте файл `config.js` в любом удобном редакторе и добавьте следующие сведения:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
};

```



### Обязательные значения

*IdentityMetadata*. Здесь модуль passport-azure-ad будет искать данные конфигурации для IdP, а также ключи для проверки маркеров JWT. Возможно, вам не требуется вносить изменения, если вы используете Azure Active Directory.

*audience* — это универсальный код ресурса (URI) перенаправления с портала.

> [AZURE.NOTE]
Мы довольно часто предоставляем свои ключи. Убедитесь, что вы всегда извлекаете данные из URL-адреса openid\_keys и что приложение имеет доступ к Интернету.


## Шаг 11. Добавление конфигурации в файл server.js

Нам нужно считать эти значения из файла конфигурации, созданного для нашего приложения. Для этого мы просто добавляем файл .config как требуемый ресурс в нашем приложении, а затем настраиваем глобальные переменные в соответствии с переменными в документе config.js

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Откройте свой файл `server.js` в любом удобном редакторе и добавьте следующие сведения:

```Javascript
var config = require('./config');
```
Затем добавьте новый раздел в `server.js` со следующим кодом:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
identityMetadata: config.creds.identityMetadata,
issuer: config.creds.issuer,
audience: config.creds.audience
};
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Шаг 12. Добавление информации о модели и схеме MongoDB с помощью Moongoose

Теперь вся эта подготовка начинает себя оправдывать, так как мы помещаем вместе следующие три файла в службу REST API.

В данном пошаговом руководстве для хранения наших задач мы будем использовать MongoDB, как описано в ***шаге 4***.

Если вы помните, применительно к файлу config.js, созданному на шаге 11, мы вызывали базу данных *tasklist*, так как именно ее мы указали в конце URL-адреса подключения mogoose\_auth\_local. Заранее создавать эту базу данных в MongoDB не нужно. Она создаст ее для нас при первом запуске нашего приложения сервера (если она еще не существует).

Теперь, когда мы указали серверу, какую базу данных MongoDB мы бы хотели использовать, необходимо написать дополнительный код для создания модели и схемы для наших задач на сервере.

#### Описание модели

Наша модель схемы является очень простой. При необходимости ее можно развернуть.

ИМЯ — имя, назначенное задаче. ***Строка***

ЗАДАЧА — сама задача. ***Строка***

Дата — дата выполнения задачи. ***Дата и время***

ЗАВЕРШЕНО — завершена задача или нет. ***ЛОГИЧЕСКИЙ***

#### Создание схемы в коде


В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Откройте свой файл `server.js` в любом удобном редакторе и добавьте следующие сведения под элементом конфигурации:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Это позволит подключиться к серверу MongoDB и вернет нам объект схемы.

#### С помощью данной схемы создайте модель в коде

Ниже созданного кода добавьте следующий код:

```Javascript
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

## Шаг 13. Добавление маршрутов для сервера REST API задачи

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

#### Добавление маршрутов по умолчанию на наш сервер

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
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
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

### Добавление процедуры обработки ошибок для маршрутов

Целесообразно добавить некоторую процедуру обработки ошибок, чтобы мы могли информировать клиента о возникающих проблемах понятным для него образом.

Добавьте следующий код под кода, который вы написали выше:

```Javascript
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


## Шаг 14. Создание сервера

Мы определили базу данных, у нас есть маршруты, и нам остается только добавить наш экземпляр сервера, который будет управлять нашими вызовами.

В Restify (и Express) имеются разнообразные возможности для настройки, которую можно выполнять для сервера REST API. Но, и в данном случае мы будем использовать самый базовый вариант настройки для наших целей.

```Javascript
/**
* Our Server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directroy TODO Server",
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
}));
```
## Шаг 15. Добавление маршрутов (в настоящее время без аутентификации)

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
## Шаг 16. Перед добавлением поддержки OAuth необходимо запустить сервер

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

Затем можно добавить задачу следующим образом:

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
Для перечисления задач для Brandon можно использовать следующий способ:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Если все это работает, мы готовы к добавлению OAuth к серверу REST API.

**У вас уже есть сервер REST API с MongoDB!**

## Шаг 17. Добавление аутентификации на сервер REST API

Теперь, когда у нас есть работающий REST API (примите наши поздравления!), сделаем его полезным для Azure AD.

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

### 1\. Использование oidcbearerstrategy, включенного в состав passport-azure-ad

Мы создали стандартный сервер REST TODO без какой-либо авторизации. Самое время добавить такую функцию.

Сначала необходимо указать, что будет использоваться Passport. Сделайте это сразу после конфигурации другого сервера:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
При записи интерфейсов API всегда нужно связывать данные с уникальными параметрами маркера, которые пользователь не сможет подделать. Когда сервер сохраняет элементы TODO, он делает это на основании идентификатора подписки пользователя в маркере (вызываемого с помощью token.sub), который мы указываем в поле owner. Таким образом только этот пользователь сможет получать доступ к своим элементам TODO. Значение owner не отображается в API, поэтому внешний пользователь может запросить элементы TODO другого пользователя, даже если для них пройдена аутентификация.

Теперь используем стратегию Open ID Connect Bearer, включенную в состав passport-azure-ad. Просмотрите код и его краткое описание ниже. Вставьте это после добавленного ранее:

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
var oidcStrategy = new OIDCBearerStrategy(options,
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
passport.use(oidcStrategy);
```

Passport использует аналогичную схему для всех своих стратегий (Twitter, Facebook и т. д.), представленных всеми модулями записи стратегий. Просматривая стратегию, можно увидеть, что мы передаем function(), у которой есть маркер, и обрабатываем параметры. Стратегия возвратится к нам после выполнения всей своей работы. После ее возврата потребуется сохранить пользователя и спрятать маркер, так как нам больше не придется его запрашивать.

> [AZURE.IMPORTANT]
Приведенный выше код принимает всех пользователей, которые прошли аутентификацию на сервере. Это называется автоматической регистрацией. На рабочих серверах не нужно разрешать другим пользователям входить без выбранной вами регистрации. Это типично для клиентских приложений, в которых разрешается регистрация через Facebook и, помимо этого, требуется ввести дополнительную информацию. Если бы это не была программа командной строки, мы могли бы извлечь электронный адрес из возвращенного объекта маркера и затем запросить дополнительную информацию. Так как это всего лишь тестовый сервер, мы просто добавим их в базу данных в памяти.

### 2. Защита некоторых конечных точек

Для защиты конечных точек необходимо указать вызов passport.authenticate() с протоколом, который вы хотите использовать.

Изменим наш маршрут в коде сервера, чтобы сделать нечто более интересное:

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

## Шаг 18. Повторный запуск приложения сервера для проверки отклонения

Снова используем `curl`, чтобы определить, если у нас сейчас защита OAuth2 применительно к нашим конечным точкам. Мы это сделаем до запуска любого клиентского пакета SDK для этой конечной точки. Возвращаемые заголовки должны содержать достаточные сведения, чтобы мы поняли, что двигаемся в правильном направлении.

Сначала убедитесь, выполняется ли ваш экземпляр monogoDB.

	$sudo mongod

Затем перейдите в каталог и запустите curling.

	$ cd azuread
	$ node server.js

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

## Дальнейшие действия

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip) или клонировать с GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Теперь можно перейти к более сложным темам. Можно попробовать:

[Защита веб-приложения Node.js с помощью конечной точки версии 2.0](active-directory-v2-devquickstarts-node-web.md)

Дополнительные ресурсы:
- [Руководство разработчика версии 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Тег StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

<!---HONumber=AcomDC_0921_2016-->
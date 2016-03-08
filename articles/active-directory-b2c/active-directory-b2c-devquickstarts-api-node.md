<properties
	pageTitle="Предварительная версия B2C: защита веб-API с помощью Node.js | Microsoft Azure"
	description="Создание веб-API Node.js, который принимает токены от клиента B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="hero-article"
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# Предварительная версия B2C: защита веб-API с помощью Node.js

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. Прочитайте [руководство по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md), чтобы ознакомиться с основными сведениями об Azure Active Directory B2C (если вы еще этого не сделали).


> [AZURE.NOTE]	Этот пример рассчитан на подключение с использованием [примера приложения iOS B2C](active-directory-b2c-devquickstarts-ios.md). Сначала изучите это руководство, а затем переходите к указанному примеру.

**Passport** — промежуточный слой проверки подлинности для Node.js. Гибкая модульная структура Passport позволяет незаметно устанавливать его в любом приложении на основе Express или в веб-приложении Restify. Полный набор стратегий поддерживает проверку подлинности с помощью имени пользователя и пароля, Facebook, Twitter и других средств. Мы разработали стратегию для Azure Active Directory (Azure AD). Сначала необходимо установить этот модуль, а затем добавить подключаемый модуль Azure AD `passport-azure-ad`.

Для этого потребуется:

1. зарегистрировать приложение в Azure AD;
2. настроить приложение для использования подключаемого модуля Passport `azure-ad-passport`.
3. настроить клиентское приложение для вызова веб-API с именем to-do list.

Код для этого учебника [размещен на портале GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs). Вы можете [загрузить заготовку приложения как ZIP-файл](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

Готовое приложение приводится в конце этого руководства.

> [AZURE.WARNING] 	Работая с предварительной версией B2C, используйте для сервера задач веб-API и для клиента, который к нему подключается, один и тот же **идентификатор клиента** или **идентификатор приложения** и политик. Это также верно для руководств по iOS и Android. Если ранее вы создали приложение, следуя инструкциям одного из этих руководств, используйте готовые значения. Новые создавать не нужно.


## Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md), прежде чем продолжить.

## Создание приложения

Затем необходимо создать приложение в каталоге B2C. Оно будет передавать в Azure AD сведения, необходимые для безопасного взаимодействия с вашим приложением. В этом случае и клиентское приложение, и веб-API будут представлены одним **идентификатором приложения**, так как они включают в себя одно приложение логики. Создайте приложение, выполнив [эти указания](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

- Включите в приложение **веб-приложение или веб-API**.
- Введите `http://localhost/TodoListService` как **URL-адрес ответа**. Это URL-адрес по умолчанию для данного примера кода.
- Создайте для своего приложения **секрет приложения** и скопируйте его. Оно понадобится вам позднее. Скоро он вам понадобится. Обратите внимание, что перед использованием это значение должно быть [экранировано для XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
- Скопируйте **идентификатор приложения**, назначенный приложению. Этот идентификатор также потребуется позднее.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Это приложение содержит три вида идентификации: регистрация, вход и вход с помощью учетной записи Facebook. Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех политик обязательно выполните указанные ниже действия.

- В политике регистрации выберите атрибут **Отображаемое имя** и другие атрибуты регистрации.
- В каждой политике выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. У него должен быть префикс `b2c_1_`. Имена политик понадобятся вам позднее.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к сборке приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Дополнительные сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Загрузка Node.js для платформы

Чтобы успешно использовать этот пример, необходимо иметь рабочую установку Node.js.

Установите Node.js с сайта [nodejs.org](http://nodejs.org).

## Установка MongoDB на платформе

Чтобы успешно использовать этот пример, также необходимо иметь рабочую установку MongoDB. Чтобы сделать интерфейс REST API постоянным для экземпляров сервера, используйте MongoDB.

Установите MongoDB с сайта [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] В данном руководстве предполагается, что вы используете стандартный установочный пакет и стандартные конечные точки сервера для MongoDB, которыми на момент написания этой статьи являются `mongodb://localhost`.

## Установка модулей Restify для веб-API

Restify будет использоваться для сборки REST API. Restify — это минимальная гибкая платформа приложений Node.j, производная от Express. Она располагает широким набором функций, позволяющих реализовать интерфейсы REST API поверх Connect.

### Установка Restify

В командной строке смените каталог на `azuread`. Если каталог `azuread` не существует, создайте его.

`cd azuread` или `mkdir azuread;`

Введите следующую команду:

`npm install restify`

Эта команда устанавливает Restify.

#### Вы получили сообщение об ошибке?

В некоторых операционных системах при использовании `npm` может произойти ошибка `Error: EPERM, chmod '/usr/local/bin/..'`, и появится запрос на запуск учетной записи с правами администратора. В этом случае необходимо с помощью команды `sudo` запустить `npm` с более высоким уровнем привилегий.

#### Вы получили сообщение об ошибке DTrace?

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

Результат этой команды должен выглядеть примерно следующим образом:

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

## Установка Passport в веб-API

[Passport](http://passportjs.org/) — промежуточный слой проверки подлинности для Node.js. Гибкая модульная структура Passport позволяет незаметно устанавливать его в любом приложении на основе Express или в веб-приложении Restify. Полный набор стратегий поддерживает проверку подлинности с помощью имени пользователя и пароля, Facebook, Twitter и других средств. Мы разработали стратегию для Azure AD. Сначала необходимо установить этот модуль, а затем добавить подключаемый модуль стратегии Azure AD.

В командной строке смените каталог на `azuread`, если это еще не сделано.

Введите следующую команду для установки Passport:

`npm install passport`

Результат этой команды должен выглядеть примерно следующим образом:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Добавление passport-azuread в веб-API

Добавьте стратегию OAuth с помощью `passport-azuread`, набора стратегий, которые устанавливают подключение Azure AD к Passport. Используйте эту стратегию для токенов носителя в примере REST API.

> [AZURE.NOTE] Несмотря на то что OAuth2 предоставляет платформу, на которой может быть выдан токен любого известного типа, широкое распространение получили токены только некоторых типов. Токены для защиты конечных точек являются токенами носителя. Это самый распространенный тип токенов, которые выдаются в OAuth2. Во многих реализациях предполагается, что токены носителя — это единственный тип выданного токена.

В командной строке смените каталог на `azuread`, если это еще не сделано.

Введите указанную ниже команду для установки модуля Passport `passport-azure-ad`:

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

## Добавление модулей MongoDB в веб-API

MongoDB будет использоваться как хранилище данных. По этой причине необходимо установить Mongoose — широко используемый подключаемый модуль для управления моделями и схемами, — а также драйвер базы данных для MongoDB, также называемый MongoDB.

* `npm install mongoose`
* `npm install mongodb`

## Установка дополнительных модулей

Теперь необходимо установить остальные требуемые модули.

В командной строке смените каталог на `azuread`, если это еще не сделано.

`cd azuread`

Введите следующие команды для установки модулей в каталоге `node_modules`:

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


## Создание файла server.js с зависимостями

Файл `server.js` предоставляет большинство функций для сервера веб-API. В этот файл будет добавлена основная часть нашего кода. Для использования в рабочей среде рекомендуется разделить функционал на небольшие файлы, такие как отдельные маршруты и контроллеры. В этом руководстве для данного функционала мы будем использовать server.js.

В командной строке смените каталог на `azuread`, если это еще не сделано.

`cd azuread`

Создайте файл `server.js` в редакторе. Добавьте следующие данные:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Сохраните файл. К нему нужно будет вернуться позже.

## Создание файла config.js для хранения параметров Azure AD

Данный файл кода передает параметры конфигурации с вашего портала Azure AD Portal в файл `Passport.js`. Эти значения конфигурации были созданы во время добавления веб-интерфейса API на портал в первой части пошагового руководства. После того как вы скопируете код, мы объясним, какие значения нужно будет указать для этих параметров.

В командной строке смените каталог на `azuread`, если это еще не сделано.

`cd azuread`

Создайте файл `config.js` в редакторе. Добавьте следующие данные:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Обязательные значения

`IdentityMetadata`: здесь `passport-azure-ad` будет искать данные конфигурации для поставщика удостоверений. Кроме того, здесь будет выполняться поиск ключей для проверки веб-токенов JSON. Если вы используете Azure AD, скорее всего, этот параметр изменять не потребуется.

`audience`: универсальный код ресурса (URI) с портала, идентифицирующий вашу службу. В нашем примере используется `http://localhost/TodoListService`.

`tenantName`: имя вашего клиента (например, **contoso.onmicrosoft.com**).

`policyName`: политика проверки токенов, поступающих на сервер. Такую же политику следует использовать для входа в клиентское приложение.

> [AZURE.NOTE] Для предварительной версии B2C используйте одни и те же политики в параметрах клиента и сервера. Если вы уже выполнили действия из пошагового руководства и создали эти политики, делать это еще раз не требуется. Поскольку вы выполнили пошаговые инструкции, нет необходимости настраивать на сайте новые политики для клиентских приложений.

## Добавление конфигурации в файл server.js

Необходимо считать эти значения из файла `config.js`, созданного для вашего приложения. Для этого добавьте файл `.config` как требуемый ресурс в вашем приложении, а затем настройте глобальные переменные для переменных в документе `config.js`.

В командной строке смените каталог на `azuread`, если это еще не сделано.

`cd azuread`

Откройте файл `server.js` в редакторе. Добавьте следующие данные:

```Javascript
var config = require('./config');
```
Добавьте в `server.js` новый раздел со следующим кодом:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Добавление сведений о модели и схеме MongoDB с помощью Moongoose

Предварительная подготовка поможет подключить эти три файла в службе REST API.

В данном пошаговом руководстве используйте MongoDB для хранения задач, как описано выше.

В созданном файле `config.js` для имени базы данных указано значение **tasklist**. Это значение добавлено в конец URL-адреса подключения `mongoose_auth_local`. Не нужно заранее создавать эту базу данных в MongoDB. База данных будет автоматически создана при первом запуске приложения сервера, если она еще не существует.

После того как вы укажете серверу, какую базу данных MongoDB необходимо использовать, вам потребуется написать дополнительный код, чтобы создать модель и схему для ваших задач на сервере.

### Развертывание узла модели

Эта модель схемы очень проста. При необходимости можно развернуть ее.

`name`: имя, назначенное задаче. Это **строка**.

`task`: сама задача. Это **строка**.

`date`: дата выполнения задачи. Это **дата и время**.

`completed`: выполнена ли задача. Это **логическое значение**.

### Создание схемы в коде

В командной строке смените каталог на `azuread`, если это еще не сделано.

`cd azuread`

Откройте файл `server.js` в редакторе. Добавьте следующие сведения под записью конфигурации:

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Это позволит подключиться к серверу MongoDB и вернет объект схемы.

### Создание модели в коде с помощью схемы

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
Сначала создайте схему, а затем — объект модели, который будет использоваться для хранения данных в коде при определении **маршрутов**.

## Добавление маршрутов для сервера задач REST API

Теперь, когда у вас есть модель базы данных для работы, добавьте маршруты, которые будете использовать для своего сервера REST API.

### О маршрутах в Restify

Маршруты работают в Restify точно так же, как при использовании стека Express. Вы определяете маршруты с помощью идентификатора URI, который, как предполагается, будут вызывать клиентские приложения. Как правило, свои маршруты вы определяете в отдельном файле. Для целей этого руководства маршруты будут помещены в файл `server.js`. Для использования в рабочей среде рекомендуем разделить их на отдельные файлы.

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

Это базовый шаблон. В Restify и Express доступны более мощные функциональные возможности, например определение типов приложений и выполнение сложной маршрутизации между разными конечными точками. В этом руководстве мы будем использовать простые маршруты.

#### Добавление на сервер маршрутов по умолчанию

Теперь нужно добавить базовые маршруты CRUD для **создания**, **извлечения**, **обновления** и **удаления**.

В командной строке смените каталог на `azuread`, если это еще не сделано.

`cd azuread`

Откройте файл `server.js` в редакторе. Добавьте следующие сведения под ранее введенными записями в базе данных:

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
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
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
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

#### Добавление обработки ошибок для маршрутов

Нужно добавить обработку ошибок, чтобы можно было передавать данные о возникших проблемах клиенту и он мог их распознавать.

Под созданным ранее кодом добавьте следующий код:

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


## Создание сервера

Теперь база данных определена, а маршруты созданы. Осталось добавить экземпляр сервера, который будет управлять вызовами.

Restify и Express предоставляют широкие возможности для настройки сервера REST API, но в данном случае будет использоваться самая простая настройка.

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
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
## Добавление маршрутов на сервер (без проверки подлинности)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
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
## Запуск сервера перед добавлением поддержки OAuth

Прежде чем добавить функцию проверки подлинности, следует протестировать сервер.

Самый простой способ для этого — использовать `curl` в командной строке. Перед этим потребуется установить простую служебную программу, которая позволяет анализировать выходные данные в формате JSON. Прежде всего установите средство JSON.

`$npm install -g jsontool`

Это обеспечивает глобальную установку средства JSON. После установки средства JSON протестируйте сервер.

Убедитесь, что ваш экземпляр MongoDB работает.

`$sudo mongodb`

Смените каталог на `azuread` и используйте `curl`.

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

Добавьте задачу:

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
Перечислить задачи для пользователя Brandon можно так:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Если все это работает, можно приступать к добавлению OAuth на сервер REST API.

У вас уже есть сервер REST API с MongoDB.

## Добавление функции проверки подлинности на сервер REST API

Теперь, когда есть работающий сервер REST API, можно подготовить его к использованию в Azure AD.

В командной строке смените каталог на `azuread`, если это еще не сделано.

`cd azuread`

### Использование стратегии OIDCBearerStrategy, включенной в passport-azure-ad

Вы создали стандартный сервер REST ToDo без функции проверки подлинности. Теперь можно начинать сборку функции проверки подлинности.

Сначала необходимо указать, что будет использоваться Passport. Добавьте эти данные непосредственно под конфигурацией другого сервера:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
При написании интерфейсов API обязательно нужно связывать данные с уникальными параметрами токена, которые пользователь не сможет подделать. Когда этот сервер сохраняет элементы ToDo, он делает это на основании **идентификатора объекта** пользователя в токене (вызываемого с помощью token.oid), который указан в поле owner. Это гарантирует, что никто, кроме пользователя, не сможет получить доступ к введенным элементам его списка ToDo. Значение owner не отображается в API, поэтому внешний пользователь может запросить элементы ToDo другого пользователя, даже если он прошел проверку подлинности.

Далее используйте стратегию носителя, которая поставляется с `passport-azure-ad`. (Пока мы просто рассмотрим код.) Вставьте это после добавленного ранее:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
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
log.info(token, 'was the token retrieved');
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

Во всех стратегиях Passport (включая Twitter и Facebook) применяется шаблон, аналогичный тому, который используют все разработчики стратегий. Вы передаете ему `function()` с `token` и `done` в качестве параметров. Стратегия будет возвращена после выполнения всех задач. Затем следует сохранить данные пользователя и токен, чтобы не задавать их в следующий раз повторно.

> [AZURE.IMPORTANT]
Приведенный выше код принимает всех пользователей, которые прошли проверку подлинности на сервере. Это называется автоматической регистрацией. На рабочих серверах не нужно разрешать другим пользователям входить в систему без прохождения выбранного вами процесса регистрации. Такой подход обычно используется для клиентских приложений, в которых разрешается регистрация с помощью учетной записи Facebook, после которой появляется запрос на ввод дополнительной информации. Если бы это не была программа командной строки, мы могли бы извлечь электронный адрес из возвращенного объекта токена и затем запросить у пользователей дополнительную информацию. Так как это всего лишь тестовый сервер, мы просто добавим пользователей в базу данных в памяти.

### Защита конечных точек

Защита конечных точек активируется, когда вы указываете вызов `passport.authenticate()` с помощью протокола, который вы собираетесь использовать.

Можно изменить маршрут в коде сервера, чтобы сделать нечто более интересное:

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

## Повторный запуск серверного приложения для подтверждения отказа в доступе

Можно снова использовать `curl`, чтобы определить, активирована ли защита OAuth2 применительно к конечным точкам. Это необходимо сделать до запуска любого клиентского пакета SDK для этой конечной точки. Возвращенные заголовки должны содержать достаточно данных, указывающих на то, что используется правильный путь.

Убедитесь, что ваш экземпляр MongoDB работает.

	$sudo mongodb

Измените каталог и используйте `curl`.

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

Если система функционирует правильно, отобразится сообщение об ошибке 401. Это означает, что уровень Passport пытается выполнить перенаправление к конечной точке авторизации.


## Теперь у вас есть служба REST API, использующая OAuth2

До сих пор вы применяли этот сервер без использования клиента, совместимого с OAuth2. Для использования такого клиента потребуются дополнительные пошаговые инструкции.

Если вам нужны только сведения о способах реализации REST API с использованием Restify и OAuth2, полученного кода достаточно, чтобы продолжать разрабатывать службу и выполнять сборку в этом примере.

Полный пример (без ваших значений конфигурации) [можно загрузить в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Дальнейшие действия

Теперь можно перейти к более сложным темам, например:

[Подключение к веб-API с помощью iOS с B2C](active-directory-b2c-devquickstarts-ios.md)

<!---HONumber=AcomDC_0302_2016-->
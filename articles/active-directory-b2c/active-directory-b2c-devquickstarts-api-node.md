---
title: "Azure AD B2C: защита веб-API с помощью Node.js | Документация Майкрософт"
description: "Создание веб-API Node.js, который принимает токены от клиента B2C"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mtillman
editor: 
ms.assetid: fc2b9af8-fbda-44e0-962a-8b963449106a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: xerners
ms.openlocfilehash: 3a0249f2f7dfd76d89cbf497376f53fe06c250c3
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C: защита веб-API с помощью Node.js
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

С помощью Azure Active Directory (Azure AD) B2C можно защитить веб-API с помощью маркера доступа OAuth 2.0. Эти маркеры позволяют клиентским приложениям, использующим Azure AD B2C, проходить проверку подлинности для API. В этой статье показано, как создать интерфейс веб-API .NET "Список дел", который позволяет пользователям добавлять и просматривать задачи. Этот веб-API защищен с помощью Azure AD B2C. Управлять списком дел могут только пользователи, прошедшие проверку подлинности.

> [!NOTE]
> Этот пример рассчитан на подключение с использованием [примера приложения iOS B2C](active-directory-b2c-devquickstarts-ios.md). Сначала изучите это руководство, а затем переходите к указанному примеру.
>
>

**Passport** — промежуточный слой проверки подлинности для Node.js. Гибкая модульная структура Passport позволяет незаметно устанавливать его в любом приложении на основе Express или в веб-приложении Restify. Полный набор стратегий поддерживает проверку подлинности с помощью имени пользователя и пароля, Facebook, Twitter и других средств. Мы разработали стратегию для Azure Active Directory (Azure AD). Сначала следует установить этот модуль, а затем добавить подключаемый модуль Azure AD `passport-azure-ad` .

Для выполнения этого примера необходимо выполнить следующие действия:

1. зарегистрировать приложение в Azure AD;
2. настроить приложение для использования подключаемого модуля Passport `passport-azure-ad` ;
3. настроить клиентское приложение для вызова веб-API с именем to-do list.

## <a name="get-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C
Перед использованием Azure AD B2C необходимо создать каталог или клиент.  Каталог — это контейнер для всех пользователей, приложений, групп и т. д.  Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md), прежде чем продолжить.

## <a name="create-an-application"></a>Создание приложения
Затем необходимо создать приложение в каталоге B2C. Оно будет передавать в Azure AD сведения, необходимые для безопасного взаимодействия с вашим приложением. В нашем примере и клиентское приложение, и веб-API представлены одним **идентификатором приложения**, так как они представляют одно приложение логики. Создайте приложение, выполнив [эти указания](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

* Включите в приложение **веб-приложение или веб-API** .
* Введите `http://localhost/TodoListService` в качестве **URL-адреса ответа**. Это URL-адрес по умолчанию для данного примера кода.
* Создайте для своего приложения **секрет приложения** и скопируйте его. Эти данные понадобятся позже. Обратите внимание, что перед использованием это значение должно быть [экранировано для XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) .
* Скопируйте **идентификатор приложения** , назначенный приложению. Эти данные понадобятся позже.

## <a name="create-your-policies"></a>Создание политик
В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Это приложение содержит два действия с удостоверениями: регистрация и вход. Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#create-a-sign-up-policy).  При создании трех политик обязательно выполните указанные ниже действия.

* В политике регистрации укажите **отображаемое имя** и другие атрибуты регистрации.
* Выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта** для каждой политики.  Можно также выбрать другие утверждения.
* Скопируйте **имя** каждой политики после ее создания. У него должен быть префикс `b2c_1_`.  Имена политик понадобятся вам позже.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к сборке приложения.

Дополнительные сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Загрузка кода
Код для этого руководства размещен на портале [GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Чтобы выполнить сборку примера, [скачайте схему проекта в ZIP-архиве](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Ее также можно клонировать:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

Кроме того, можно скачать готовое приложение [в виде ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) или получить его из ветви `complete` того же репозитория.

## <a name="download-nodejs-for-your-platform"></a>Загрузка Node.js для платформы
Чтобы этот пример у вас заработал, потребуется рабочая установка Node.js.

Установите Node.js с сайта [nodejs.org](http://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Установка MongoDB на платформе
Чтобы этот пример у вас заработал, потребуется рабочая установка MongoDB. MongoDB мы используем для того, чтобы интерфейс REST API устойчиво работал с несколькими экземплярами сервера.

Установите MongoDB с сайта [mongodb.org](http://www.mongodb.org).

> [!NOTE]
> В данном руководстве предполагается, что вы используете стандартный установочный пакет и стандартные конечные точки сервера для MongoDB. На момент написания статьи это `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Установка модулей Restify для веб-API
Restify мы используем для сборки REST API. Restify — это минимальная гибкая платформа приложений Node.j, производная от Express. Она располагает широким набором функций, позволяющих реализовать интерфейсы REST API поверх Connect.

### <a name="install-restify"></a>Установка Restify
В командной строке перейдите в каталог `azuread`. Если каталог `azuread` не существует, создайте его.

`cd azuread` или `mkdir azuread;`

Введите следующую команду:

`npm install restify`

Эта команда устанавливает Restify.

#### <a name="did-you-get-an-error"></a>Вы получили сообщение об ошибке?
В некоторых операционных системах при использовании `npm` вы можете увидеть ошибку `Error: EPERM, chmod '/usr/local/bin/..'` и предложение запустить учетную запись с правами администратора. В этом случае необходимо с помощью команды `sudo` запустить `npm` с более высоким уровнем привилегий.

#### <a name="did-you-get-a-dtrace-error"></a>Вы получили сообщение об ошибке DTrace?
При установке Restify вы можете увидеть что-то подобное:

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

## <a name="install-passport-in-your-web-api"></a>Установка Passport в веб-API
В командной строке перейдите в каталог `azuread`, если он еще не выбран.

Установите Passport с помощью следующей команды:

`npm install passport`

Результат этой команды должен выглядеть примерно следующим образом:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Добавление passport-azuread в веб-API
Добавьте стратегию OAuth с помощью набора стратегий `passport-azuread`, который устанавливает подключение Azure AD к Passport. Используйте эту стратегию для токенов носителя в примере REST API.

> [!NOTE]
> Несмотря на то что OAuth2 предоставляет платформу, на которой может быть выдан токен любого известного типа, широкое распространение получили токены только некоторых типов. Токены для защиты конечных точек являются токенами носителя. Это самый распространенный тип токенов, которые выдаются в OAuth2. Во многих реализациях предполагается, что токены носителя — это единственный тип выданного токена.
>
>

В командной строке перейдите в каталог `azuread`, если он еще не выбран.

Введите указанную ниже команду для установки модуля Passport `passport-azure-ad` :

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

## <a name="add-mongodb-modules-to-your-web-api"></a>Добавление модулей MongoDB в веб-API
В этом примере для хранения данных используется MongoDB. Для этого нужно установить Mongoose, широко используемый подключаемый модуль для управления моделями и схемами.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Установка дополнительных модулей
Теперь необходимо установить остальные требуемые модули.

В командной строке перейдите в каталог `azuread`, если он еще не выбран.

`cd azuread`

Установите модули в каталог `node_modules` :

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>Создание файла server.js с зависимостями
Основная часть функций для сервера веб-API реализована в файле `server.js` .

В командной строке перейдите в каталог `azuread`, если он еще не выбран.

`cd azuread`

Создайте в текстовом редакторе файл `server.js` . Добавьте следующие данные:

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

Сохраните файл. Он нам понадобится позже.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Создание файла config.js для хранения параметров Azure AD
Данный фрагмент кода передает параметры конфигурации с вашего портала Azure AD в файл `Passport.js` . Эти значения конфигурации были созданы во время добавления веб-интерфейса API на портал в первой части пошагового руководства. Сейчас мы объясним, какие значения нужно указать для этих параметров в скопированном фрагменте кода.

В командной строке перейдите в каталог `azuread`, если он еще не выбран.

`cd azuread`

Создайте в текстовом редакторе файл `config.js` . Добавьте следующие данные:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Обязательные значения
`clientID`: идентификатор клиента для приложения веб-API.

`IdentityMetadata`: здесь `passport-azure-ad` будет искать данные конфигурации для поставщика удостоверений. Кроме того, здесь будет выполняться поиск ключей для проверки веб-токенов JSON.

`audience`: универсальный код ресурса (URI), который идентифицирует вашу службу. Его нужно взять на портале.

`tenantName`: имя клиента (например, **contoso.onmicrosoft.com**).

`policyName`: политика проверки токенов, поступающих на сервер. Здесь нужно указать ту же политику, которую вы используете для входа в клиентское приложение.

> [!NOTE]
> Пока что используйте одни и те же политики в параметрах клиента и сервера. Если вы уже выполнили действия из пошагового руководства и создали эти политики, делать это еще раз не требуется. Поскольку вы выполнили пошаговые инструкции, нет необходимости настраивать на сайте новые политики для клиентских приложений.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Добавление конфигурации в файл server.js
Чтобы получить значения из файла `config.js`, добавьте в свое приложение файл `.config` в качестве обязательного ресурса, а затем настройте глобальные переменные для переменных, содержащихся в документе `config.js`.

В командной строке перейдите в каталог `azuread`, если он еще не выбран.

`cd azuread`

Откройте файл `server.js` в редакторе. Добавьте следующие данные:

```Javascript
var config = require('./config');
```
Добавьте в `server.js` новый раздел со следующим кодом:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

Теперь добавьте заполнители для пользователей, которых мы получаем от наших вызывающих приложений.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Также создадим средство ведения журнала.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Добавление сведений о модели и схеме MongoDB с помощью Moongoose
Все, что мы сделали ранее, теперь поможет нам подключить все три файла к службе REST API.

В данном пошаговом руководстве используйте MongoDB для хранения задач, как описано выше.

В файле `config.js` для имени базы данных указано значение **tasklist**. Это же значение указывается в конце URL-адреса для подключения `mongoose_auth_local` . Не нужно заранее создавать эту базу данных в MongoDB. База данных будет автоматически создана при первом запуске приложения сервера.

После того как вы укажете серверу, какую базу данных MongoDB необходимо использовать, вам потребуется написать дополнительный код, чтобы создать модель и схему для ваших задач на сервере.

### <a name="expand-the-model"></a>Развертывание узла модели
Эта модель схемы очень проста. При необходимости можно развернуть ее.

`owner`: кому назначено это задание. Значение указывается в формате **string**(строка).  

`Text`: сама задача. Значение указывается в формате **string**(строка).

`date`: дата ожидаемого выполнения задачи. Значение указывается в формате **datetime**(дата и время).

`completed`: статус завершения задачи. Значение указывается в формате **Boolean**(логическое).

### <a name="create-the-schema-in-the-code"></a>Создание схемы в коде
В командной строке перейдите в каталог `azuread`, если он еще не выбран.

`cd azuread`

Откройте файл `server.js` в редакторе. Добавьте следующие сведения под записью конфигурации:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Сначала создайте схему, а затем — объект модели для хранения данных, который вы используете в коде при определении **маршрутов**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Добавление маршрутов для сервера задач REST API
Теперь модель базы данных готова к использованию. Добавьте маршруты, которые используются для сервера REST API.

### <a name="about-routes-in-restify"></a>О маршрутах в Restify
Маршруты работают в Restify точно так же, как при использовании стека Express. Вы определяете маршруты с помощью идентификатора URI, который, как предполагается, будут вызывать клиентские приложения.

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

В Restify и Express доступны более мощные функциональные возможности, например определение типов приложений и выполнение сложной маршрутизации между разными конечными точками. В этом руководстве мы будем использовать только простые маршруты.

#### <a name="add-default-routes-to-your-server"></a>Добавление на сервер маршрутов по умолчанию
Теперь добавьте базовые маршруты CRUD для операций **create** (создание) и **list**(список) интерфейса REST API. Другие маршруты можно найти в ветви `complete` примера.

В командной строке перейдите в каталог `azuread`, если он еще не выбран.

`cd azuread`

Откройте файл `server.js` в редакторе. Добавьте следующие сведения под записями о базе данных, которые вы добавили ранее:

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

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
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
```

```Javascript
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


#### <a name="add-error-handling-for-the-routes"></a>Добавление обработки ошибок для маршрутов
Добавьте обработку ошибок, чтобы в понятной форме передавать клиенту данные о возникших проблемах.

Добавьте следующий код:

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


## <a name="create-your-server"></a>Создание сервера
Теперь база данных определена, а маршруты созданы. Осталось только добавить экземпляр сервера, который обрабатывает вызовы.

Restify и Express предоставляют широкие возможности для настройки сервера REST API, но здесь мы используем самый простой вариант.

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
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Добавление маршрутов на сервер (без проверки подлинности)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Добавление функции проверки подлинности на сервер REST API
Теперь, когда есть работающий сервер REST API, можно подготовить его к использованию в Azure AD.

В командной строке перейдите в каталог `azuread`, если он еще не выбран.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Использование стратегии OIDCBearerStrategy, включенной в passport-azure-ad
> [!TIP]
> При написании интерфейсов API обязательно нужно связывать данные с уникальными параметрами токена, которые пользователь не сможет подделать. Этот сервер сохраняет элементы ToDo на основании **идентификатора объекта** пользователя, указанного в токене (свойство token.oid). Это значение сохраняется в поле owner. Благодаря этому только владелец сможет получить доступ к своим элементам ToDo. Значение owner не отображается в API, поэтому внешний пользователь может запросить элементы ToDo другого пользователя, даже если он прошел проверку подлинности.
>
>

Далее используйте стратегию носителя, которая поставляется с `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
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
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport использует стандартный шаблон для всех своих стратегий. Вы передаете ему функцию `function()` с параметрами `token` и `done`. Стратегия будет возвращена после выполнения всех задач. Затем следует сохранить данные пользователя и токен, чтобы не задавать их в следующий раз повторно.

> [!IMPORTANT]
> Приведенный выше код принимает всех пользователей, которые прошли проверку подлинности на сервере. Этот процесс называется автоматической регистрацией. На рабочих серверах нельзя разрешать пользователям использовать API без предварительной регистрации. Такой подход обычно используется для клиентских приложений, которые позволяют войти с помощью учетной записи Facebook, а затем предлагают ввести дополнительную информацию. В других вариантах, кроме запуска программы из командной строки, адрес электронной почты можно получить из возвращенного объекта токена, а после этого запросить у пользователя дополнительную информацию. В нашем упрощенном примере мы сохраняем их в базу данных в памяти.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Запуск серверного приложения для подтверждения отказа в доступе
Вы можете использовать `curl` , чтобы определить, активирована ли защита OAuth2 применительно к конечным точкам. Возвращенные заголовки должны подтвердить, что пока все сделано правильно.

Убедитесь, что ваш экземпляр MongoDB работает.

    $sudo mongodb

Перейдите в нужный каталог и запустите сервер:

    $ cd azuread
    $ node server.js

Запустите команду `curl`

Попробуйте выполнить базовую команду POST:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Если система функционирует правильно, отобразится сообщение об ошибке 401. Это означает, что уровень Passport пытается выполнить перенаправление к конечной точке авторизации.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Теперь у вас есть служба REST API, использующая OAuth2
Вы завершили создание REST API с использованием Restify и OAuth. Этого кода будет достаточно, чтобы продолжить создание службы на основе предложенного примера. До сих пор вы применяли этот сервер без использования клиента, совместимого с OAuth2. На следующем этапе вам пригодятся дополнительные инструкции, которые есть, например, в пошаговом руководстве [Azure AD B2C: вызов веб-API из приложения iOS с использованием сторонней библиотеки](active-directory-b2c-devquickstarts-ios.md) .

## <a name="next-steps"></a>Дальнейшие действия
Теперь можно перейти к более сложным темам.

[Подключение к веб-API с помощью iOS с B2C](active-directory-b2c-devquickstarts-ios.md)

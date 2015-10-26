<properties
	pageTitle="Приступая к работе с Azure AD NodeJS | Microsoft Azure"
	description="Практическое руководство по созданию веб-интерфейса API с помощью Node.js, которое интегрируется с Azure AD для проверки подлинности."
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
	ms.date="10/13/2015"
	ms.author="brandwe"/>

# Приступая к работе с веб-интерфейсом API для узла

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

В этом пошаговом руководстве описывается быстрый и простой способ настройки службы REST API, интегрированной с Azure Active Directory для защиты API с помощью протокола OAuth2. Пример сервера, применяемого при загрузке, предназначен для запуска на любой платформе, кроме целевой системы OSX и Linux.

По окончании работы с этим пошаговым руководством вы сможете построить работающий сервер REST API со следующими возможностями:

* сервер Node.js, в котором выполняется интерфейс REST API с JSON и используется MongoDB в качестве постоянного хранилища;
* интерфейсы API на основе REST, в которых применяется защита OAuth2 с маркерами носителя при использовании Azure Active Directory.


Мы выпустили весь исходный код для данного рабочего примера в GitHub на условиях лицензии Apache 2.0, поэтому вы можете спокойно клонировать (или даже разветвлять!), предоставлять отзывы и запросы на получение.

## О модулях Node.js

В настоящем пошаговом руководстве мы будем использовать модули Node.js. Модули — это загружаемые пакеты JavaScript, дающую приложению определенную функциональность. Обычно вы устанавливаете модули с помощью средства командной строки Node.js NPM в каталоге установки NPM, но некоторые модули, например модуль HTTP, содержатся в основном пакете Node.js. Установленные модули сохраняются в каталоге node\_modules в корневом каталоге установки Node.js. В каждом модуле в каталоге node\_modules содержится свой каталог node\_modules, в котором находятся модули-зависимости, и в каждом требуемом модуле имеется каталог node\_modules. Такая рекурсивная структура каталога представляет цепочку зависимостей.

Структура цепочки зависимостей приводит к увеличению объема приложения, но гарантирует, что все зависимости учтены и что версия модулей, используемых в процессе разработки, также будет применяться в производственной среде. Это делает более предсказуемым поведение рабочего приложения и предотвращает проблемы с управлением версиями, которые могут повлиять на работу пользователей.

## Шаг 1. Регистрация клиента Azure AD

Для использования этого примера потребуется клиент Azure Active Directory. Если вы не знаете точно, что такое клиент или как можно его получить, перейдите к разделу [Как получить клиент Azure AD](active-directory-howto-tenant.md).

## Шаг 2. Добавление веб-интерфейса API к вашему клиенту

После получения своего клиента Azure Active Directory добавьте этот пример приложения к своему клиенту, чтобы его можно было использовать для защиты API.

Чтобы ваше приложение могло осуществлять проверку подлинности пользователей, сначала необходимо зарегистрировать новое приложение в своем клиенте.

- Выполните вход на портале управления Azure.
- В левой панели навигации нажмите **Active Directory**.
- Выберите клиент, в котором нужно зарегистрировать приложение.
- Перейдите во вкладку **Приложения** и нажмите кнопку Добавить в нижней панели.
- Следуйте инструкциям на экране, а затем создайте новое **веб-приложение и/или WebAPI**.
    - **Имя** приложения отображает его описание конечным пользователям.
    -	**URL-адрес входа** — это базовый URL-адрес вашего приложения. Схема по умолчанию — `https://localhost:8888`.
    - **URI идентификатора приложения** — это уникальный идентификатор вашего приложения. Соглашение заключается в использовании `https://<tenant-domain>/<app-name>`, например, `https://contoso.onmicrosoft.com/my-first-aad-app`
- После завершения регистрации Azure AD назначает приложению уникальный идентификатор клиента. Это значение понадобится в следующих разделах, поэтому его стоит скопировать из вкладки «Настройка».

## Шаг 3. Загрузка node.js для своей платформы
Чтобы успешно использовать этот пример, необходимо иметь рабочую установку Node.js.

Установите Node.js с сайта [http://nodejs.org](http://nodejs.org).

## Шаг 4. Установка MongoDB на вашей платформе

Чтобы успешно использовать этот пример, необходимо иметь рабочую установку MongoDB. Мы будем использовать MongoDB, чтобы сделать наш интерфейс REST API постоянным для экземпляров сервера.

Установите MongoDB с сайта [http://mongodb.org](http://www.mongodb.org).

**Примечание.** Данное пошаговое руководство предполагает, что вы используете заданные по умолчанию установку и конечные точки сервера для MongoDB, которыми на момент написания этой статьи являются: mongodb://localhost


## Шаг 5. Установка модулей Restify для вашего веб-интерфейса API

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


## Шаг 6. Установка Passport.js в веб-интерфейсе API

[Passport](http://passportjs.org/) — промежуточный слой проверки подлинности для Node.js. Он имеет очень гибкую и модульную структуру, которая позволяет сравнительно незаметно размещать данный слой в любом приложении на основе Express или в веб-приложении Resitify. Полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Azure Active Directory. Мы установим этот модуль, а затем добавим подключаемый модуль стратегии Azure Active Directory.

В командной строке перейдите в каталог azuread.

Введите следующую команду для установки passport.js

`npm install passport`

Результат этой команды должен выглядеть примерно следующим образом:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Шаг 7. Добавление поддержки маркеров носителя Passport.js веб-API

Далее мы добавим стратегию носителя с помощью passport-bearer-http, представляющего собой обработчик Bearner для [Passport](http://passportjs.org/). Также мы добавим поддержку обработчика маркеров JWT с помощью node-jwt.

**Примечание.** Несмотря на то что OAuth2 предоставляет платформу, в которой может быть выдан маркер любого известного типа, широкое распространение получили маркеры только некоторых типов. В результате для защиты конечных точек появились маркеры носителя. Маркеры носителя относятся типу наиболее часто выдаваемых маркеров в OAuth2, и во многих реализациях предполагается, что выдаются только маркеры данного типа.

В командной строке перейдите в каталог **azuread**.

Введите следующую команду для установки Passport.js

- `npm install passport-oauth`
- `npm install passport-http-bearer`
- `npm install node-jwt`

Результат этой команды должен выглядеть примерно следующим образом:

	ms-passport-wsfed-saml2@0.3.8 node_modules\passport-oauth  
	├── xtend@2.0.3
	├── xml-crypto@0.0.9
	├── xmldom@0.1.13
	└── xml2js@0.1.14 (sax@0.5.2)


## Шаг 8. Добавление модулей MongoDB в веб-интерфейс API

Мы будем использовать MongoDB в качестве хранилища данных. По этой причине нам нужно установить широко используемый подключаемый модуль для управления моделями и схемами, которые называются Mongoose, а также драйвер базы данных для MongoDB, также называемый MongoDB.


* `npm install mongoose`
* `npm install mongodb`

## Шаг 9. Установка дополнительных модулей

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


## Шаг 10. Создание server.js с зависимостями

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
```

Сохраните файл. Мы вернемся к нему позже.

## Шаг 11. Создание файла конфигурации для сохранения параметров Azure AD

Данный файл кода передает параметры конфигурации из вашего портала Azure Active Directory в Passport.js. Эти значения конфигурации были созданы при добавлении веб-интерфейса API на портал в первой части настоящего пошагового руководства. Мы объясним, что нужно будет сделать со значениями этих параметров после копирования кода.


В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Создайте файл `config.js` в любом удобном редакторе и добавьте следующие сведения:

```Javascript
// Don't commit this file to your public repos
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
    openid_configuration: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
    openid_keys: 'https://login.microsoftonline.com/common/discovery/keys', // For using Microsoft you should never need to change this. If absent will attempt to get from openid_configuration
}

```



**Примечание.** Скорее всего, вам никогда не потребуется изменять эти значения.

**Примечание.** Мы довольно часто предоставляем свои ключи. Убедитесь, что вы всегда извлекаете данные из URL-адреса openid\_keys и что приложение имеет доступ к Интернету.


## Шаг 12: Добавление конфигурации в файл server.js

Нам нужно считать эти значения из файла конфигурации, созданного для нашего приложения. Для этого мы просто добавляем файл .config как требуемый ресурс в нашем приложении, а затем настраиваем глобальные переменные в соответствии с переменными в документе config.js

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Откройте свой файл `server.js` в любом удобном редакторе и добавьте следующие сведения:

```Javascript
var config = require('./config');
```
Затем добавьте новый раздел в `server.js` со следующим кодом:

```Javascript
/**
* Setup some configuration
*/
var mongoose = require('mongoose/');
var serverPort = process.env.PORT || 8888;
var serverURI = ( process.env.PORT ) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

```
## Шаг 13. Создание вспомогательного файла metadata.js в качестве вспомогательного средства синтаксического анализа метаданных или маркеров

Поскольку целью является сохранение только логики приложения в файле server.js, целесообразно разместить некоторые вспомогательные методы в отдельном файле. Эти методы просто помогают анализировать метаданные OpenID Connect и не связаны с основным сценарием. Лучше хранить их отдельно. Мы будем расширять этот файл по мере изучения пошагового руководства.

***Примечание.*** Вы можете заметить, что этот файл metadata.js выполняет синтаксический анализ XML для SAML и WS-Fed, а также JSON для OpenID Connect. Это сделано намеренно, и вы будете также использовать данный файл в других наших примерах. На данном этапе файл можно игнорировать.

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Создайте файл `metadata.js` в любом удобном редакторе и добавьте следующие сведения:

```Javascript

'use strict';

var xml2js = require('xml2js');
var request = require('request');
var aadutils = require('./aadutils');
var async = require('async');

// Logging

var bunyan = require('bunyan');
var log = bunyan.createLogger({name: 'Microsoft OpenID Connect Passport Strategy'});

var Metadata = function (url, authtype) {


  if(!url) {
    throw new Error("Metadata: url is a required argument");
  }
  if(!authtype) {
    throw new Error('OIDCBearerStrategy requires an authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"');
  }

  this.url = url;
  this.metadata = null;
  this.authtype = authtype;
  log.info(authtype, 'Metadata requested for authentication type');
};

Object.defineProperty(Metadata, 'url', {
  get: function () {
    return this.url;
  }
});

Object.defineProperty(Metadata, 'saml', {
  get: function () {
    return this.saml;
  }
});

Object.defineProperty(Metadata, 'wsfed', {
  get: function () {
    return this.wsfed;
  }
});

Object.defineProperty(Metadata, 'oidc', {
  get: function () {
    return this.oidc;
  }
});


Object.defineProperty(Metadata, 'metadata', {
  get: function () {
    return this.metadata;
  }
});

Metadata.prototype.updateSamlMetadata = function(doc, next) {
  log.info('Request to update the SAML Metadata');
  try {

    this.saml = {};

    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var idp = aadutils.getElement(entity, 'IDPSSODescriptor');
    var signOn = aadutils.getElement(idp[0], 'SingleSignOnService');
    var signOff = aadutils.getElement(idp[0], 'SingleLogoutService');
    var keyDescriptor = aadutils.getElement(idp[0], 'KeyDescriptor');
    this.saml.loginEndpoint = signOn[0].$.Location;
    this.saml.logoutEndpoint = signOff[0].$.Location;

    // copy the x509 certs from the metadata
    this.saml.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.saml.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid SAMLP Federation Metadata ' + e.message));
  }
};

Metadata.prototype.updateOidcMetadata = function(doc, next) {
  log.info('Request to update the Open ID Connect Metadata');
  try {
    this.oidc = {};

    var issuer = doc['issuer'];
    var keyDescriptor = aadutils.getElement(idp[0], 'keys');

    // copy the x509 certs from the metadata
    this.oidc.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.oidc.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid Open ID Connect Federation Metadata ' + e.message));
  }
};


Metadata.prototype.updateWsfedMetadata = function(doc, next) {
  log.info('Request to update the WS Federation Metadata');
  try {
    this.wsfed = {};
    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var roles = aadutils.getElement(entity, 'RoleDescriptor');
    for(var i = 0; i < roles.length; i++) {
      var role = roles[i];
      if(role['fed:SecurityTokenServiceEndpoint']) {
        var endpoint = role['fed:SecurityTokenServiceEndpoint'];
        var endPointReference = aadutils.getFirstElement(endpoint[0],'EndpointReference');
        this.wsfed.loginEndpoint = aadutils.getFirstElement(endPointReference,'Address');

        var keyDescriptor = aadutils.getElement(role, 'KeyDescriptor');
        // copy the x509 certs from the metadata
        this.wsfed.certs = [];
        for (var j=0;j<keyDescriptor.length;j++) {
          this.wsfed.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
        }
        break;
      }
    }

    return next(null);
  } catch (e) {
    next(new Error('Invalid WSFED Federation Metadata ' + e.message));
  }
};

Metadata.prototype.fetch = function(callback) {
  var self = this;
  log.info("Fetching metadata from the provided metadata URL: " + self.url);
  async.waterfall([
    // fetch the Federation metadata for the AAD tenant
    function(next){
      request(self.url, function (err, response, body) {
        if(err) {
          next(err);
        } else if(response.statusCode !== 200) {
          next(new Error("Error:" + response.statusCode +  " Cannot get AAD Federation metadata from " + self.url));
        } else {
          log.info(body, "retreived");
          next(null, body);
        }
      });
    },
    function(body, next){
      // parse the AAD Federation metadata xml

      if(self.authtype == "saml" || self.authtype == "wsfed") {
      log.info(body, "Parsing XML retreived from the endpoint");
      var parser = new xml2js.Parser({explicitRoot:true});
      // Note: xml responses from Azure AAD have a leading \ufeff which breaks xml2js parser!
      parser.parseString(body.replace("\ufeff", ""), function (err, data) {
        self.metatdata = data;
        next(err);

      });
    } else if(self.authtype == "oidc") {
      log.info(body, "Parsing JSON retreived from the endpoint");
      JSON.parse(body, function (err, data) {
        self.metatdata = data;
        next(err);
      });

    } else {

       next(new Error("Error: No Authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"));
    }

    },

    function(next){
      if(self.authtype = "saml") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateSamlMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "wsfed") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateWsfedMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "oidc") {
      self.updateOidcMetadata(self.metadata, next);
    }},
  ], function (err) {
    // return err or success (err === null) to callback
    callback(err);
  });
};

exports.Metadata = Metadata;
```
Как видно из кода, файл просто берет URL-адрес openid, переданный в `config.js`, а затем ищет в нем информацию, которая будет использоваться в файле `server.js`. Настоятельно рекомендуем проанализировать этот код и, при необходимости, расширить его .

### Загрузите файл metadata.js на свой server.js

Мы должны сообщить нашему серверу о том, где можно получить только что созданные методы.

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

Откройте свой файл `server.js` в любом удобном редакторе и добавьте следующие сведения:

```Javascript
var metadata = require('./metadata);
```
Добавьте в конец раздела `Configuration` этот вызов для отправки документа с метаданными в нашем `config.js` в синтаксический анализатор, который мы только что написали:

```Javascript
this.aadutils = new var Metadata = require('./metadata').Metadata;
```

## Шаг 14. Добавление сведений о модели и схеме MongoDB с помощью Moongoose

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
/**
*
* Connect to MongoDB
*/

global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;  
```
Это позволит подключиться к серверу MongoDB и вернет нам объект схемы.

#### С помощью данной схемы создайте модель в коде

Ниже созданного кода добавьте следующий код:

```Javascript
/**
/ Here we create a schema to store our tasks. Pretty simple schema for now.
*/

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

## Шаг 15. Добавление маршрутов для сервера REST API задачи

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
 * APIs
 */

function createTask(req, res, next) {

	// Resitify currently has a bug which doesn't allow you to set default headers
  	// This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
  var _task = new Task();

        if (!req.params.task) {
                req.log.warn('createTask: missing task');
                next(new MissingTaskError());
                return;
        }


  _task.owner = req.params.owner;
   _task.task = req.params.task;
   _task.date = new Date();

  _task.save(function (err) {
  	if (err) {
        req.log.warn(err, 'createTask: unable to save');
        next(err);
    } else {
    res.send(201, _task);

			}
  });

  return next();

}


/**
 * Deletes a Task by name
 */
function removeTask(req, res, next) {

        Task.remove( { task:req.params.task }, function (err) {
                if (err) {
                        req.log.warn(err,
                                     'removeTask: unable to delete %s',
                                     req.params.task);
                        next(err);
                } else {
                        res.send(204);
                        next();
                }
        });
}

/**
 * Deletes all Tasks. A wipe
 */
function removeAll(req, res, next) {
        Task.remove();
        res.send(204);
        return next();
}    });
}


/**
 *
 *
 *
 */
function getTask(req, res, next) {


        Task.find(req.params.name, function (err, data) {
                if (err) {
                        req.log.warn(err, 'get: unable to read %s', req.params.name);
                        next(err);
                        return;
                }

                res.json(data);
        });

        return next();
}


/**
 * Simple returns the list of TODOs that were loaded.
 *
 */

function listTasks(req, res, next) {
  // Resitify currently has a bug which doesn't allow you to set default headers
  // This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

  console.log("server getTasks");

  Task.find().limit(20).sort('date').exec(function (err,data) {

    if (err)
      return next(err);

    if (data.length > 0) {
            console.log(data);
        }

    if (!data.length) {
            console.log('there was a problem');
            console.log(err);
            console.log("There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

    else {

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


function TaskExistsError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 409,
                restCode: 'TaskExists',
                message: name + ' already exists',
                constructorOpt: TaskExistsError
        });

        this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 404,
                restCode: 'TaskNotFound',
                message: name + ' was not found',
                constructorOpt: TaskNotFoundError
        });

        this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## Шаг 16. Создание сервера!

Мы определили базу данных, у нас есть маршруты, и нам остается только добавить наш экземпляр сервера, который будет управлять нашими вызовами.

В Restify (и Express) имеются разнообразные возможности для настройки, которую можно выполнять для сервера REST API. Но, и в данном случае мы будем использовать самый базовый вариант настройки для наших целей.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
        name: "Azure Active Directroy TODO Server",
    version: "1.0.0",
    formatters: {
        'application/json': function(req, res, body){
            if(req.params.callback){
                var callbackFunctionName = req.params.callback.replace(/[^A-Za-z0-9_\.]/g, '');
                return callbackFunctionName + "(" + JSON.stringify(body) + ");";
            } else {
                return JSON.stringify(body);
            }
        },
        'text/html': function(req, res, body){
            if (body instanceof Error)
                        return body.stack;

                      if (Buffer.isBuffer(body))
                        return body.toString('base64');

                return util.inspect(body);
        },
        'application/x-www-form-urlencoded': function(req, res, body){
            if (body instanceof Error) {
                    res.statusCode = body.statusCode || 500;
                    body = body.message;
            } else if (typeof (body) === 'object') {
                body = body.task || JSON.stringify(body);
            } else {
                body = body.toString();
            }

        res.setHeader('Content-Length', Buffer.byteLength(body));
        return (body);
        }
    }
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

        // This lets us push JSON to the REST API endpoint as well. Maps x: y as /name:value

        server.use(restify.bodyParser({ mapParams: false }));

        /// Now the real handlers. Here we just CRUD

        server.get('/tasks', listTasks);
        server.head('/tasks', listTasks);
        server.get('/tasks/:name', getTask);
        server.head('/tasks/:name', getTask);
        server.post('/tasks/:name/:task', createTask);
        server.del('/tasks/:name/:task', removeTask);
        server.del('/tasks/:name', removeTask);
        server.del('/tasks', removeAll, function respond(req, res, next) { res.send(204); next(); });


        // Register a default '/' handler

        server.get('/', function root(req, res, next) {
                var routes = [
                        'GET     /',
                        'POST    /tasks/:name/:task',
                        'GET     /tasks',
                        'PUT     /tasks/:name',
                        'GET     /tasks/:name',
                        'DELETE  /tasks/:name/:task'
                ];
                res.send(200, routes);
                next();
        });

  server.listen(serverPort, function() {

  var consoleMessage = '\n Azure Active Directory Tutorial'
  consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++'
  consoleMessage += '\n %s server is listening at %s';
  consoleMessage += '\n Open your browser to %s/tasks\n';
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n'
  consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n'
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n'  

  console.log(consoleMessage, server.name, server.url, server.url, server.url);

});
```

## Этап 17. Перед добавлением поддержки OAuth необходимо запустить сервер.

Перед переходом к части пошагового руководства, связанной с OAuth, рекомендуется убедиться, что нет ошибок.

Самый простой способ для этого — использовать `curl` в командной строке. Перед этим нам потребуется простая служебная программа, которая позволяет анализировать выходные данные в формате JSON. Для этого необходимо установить средство [json](https://github.com/trentm/json), так как оно применяется во всех примерах, приведенных ниже.

	$npm install -g jsontool

Это обеспечивает глобальную установку средства JSON. Теперь, когда мы это сделали, вернемся к серверу:

Сначала убедитесь, выполняется ли ваш экземпляр monogoDB.

	$sudo mongod

Затем перейдите в каталог и запустите curling.

	$ cd azuread
	$ node server.js

	$ curl -isS http://127.0.0.1:8888 | json
	HTTP/1.1 200 OK
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 145
	Date: Wed, 29 Jan 2014 03:41:24 GMT

	[
  	"GET     /",
  	"POST    /tasks/:owner/:task",
  	"GET     /tasks",
  	"DELETE  /tasks",
  	"PUT     /tasks/:owner",
  	"GET     /tasks/:owner",
  	"DELETE  /tasks/:task"
	]

Затем можно добавить задачу следующим образом:

	$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello

Ответ должен быть следующим:

	HTTP/1.1 201 Created
	Connection: close
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Headers: X-Requested-With
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 5
	Date: Tue, 04 Feb 2014 01:02:26 GMT

	Hello

Для перечисления задач для Brandon можно использовать следующий способ:

	$ curl -isS http://127.0.0.1:8888/tasks/brandon/

Если все это работает, мы готовы к добавлению OAuth к серверу REST API.

## Шаг 18. Добавление кода Passport.js на сервер REST API

Теперь, когда у нас есть работающий REST API (примите наши поздравления!), сделаем его полезным для Azure AD.

В окне командной строки перейдите в каталог **azuread**, если вы еще в нем не находитесь:

`cd azuread`

### Шаг 1. Добавление наших модулей Passport

Откройте свой файл `server.js` в любом удобном редакторе и добавьте следующие сведения ниже в том месте, где вы ранее уже указали модули для загрузки: Это должно находиться в верхней части файла непосредственно после импорта `var aadutils = require('./aadutils');`.

```Javascript
/*
*
* Load our old friend Passport for OAuth2 flows
*/

var passport = require('passport')
  , OAuth2Strategy = require('passport-oauth').OAuth2Strategy;
```

### 2\. Информирование сервера, что используется проверка подлинности

Откройте свой файл `server.js` в любом удобном редакторе и добавьте следующие сведения **под server.get()**, где вы определили свои маршруты, но выше метода **server.listen()**.


Нужно сообщить Restify, что можно начать использовать его `authorizationParser()` и смотреть содержимое заголовка авторизации.

```Javascript
        server.use(restify.authorizationParser());


```


### 3\. Добавление модуля Passport OAuth2 в код

Здесь мы используем определенные параметры OAuth2, которые были добавлены в файл config.js. Если наш файл `aadutils.js` выполнил синтаксический анализ нашего документа метаданных федерации, все эти значения должны быть указаны для нас даже в том случае, если они не указаны в файле config.js.

```Javascript
// Now our own handlers for authentication/authorization
// Here we only use Oauth2 from Passport.js

passport.use('provider', new OAuth2Strategy({
    authorizationURL: authEndpoint,
    tokenURL: tokenEndpoint,
    clientID: clientID,
    clientSecret: clientSecret,
    callbackURL: callbackURL
  },
  function(accessToken, refreshToken, profile, done) {
    User.findOrCreate({ UserId: profile.id }, function(err, user) {
      done(err, user);
    });
  }
));

// Let's start using Passport.js

server.use(passport.initialize());

```
### Шаг 4. Добавление маршрутов для проверки подлинности OAuth

```Javascript
// Redirect the user to the OAuth 2.0 provider for authentication.  When
// complete, the provider will redirect the user back to the application at
//     /auth/provider/callback

server.get('/auth/provider', passport.authenticate('provider'));

// The OAuth 2.0 provider has redirected the user back to the application.
// Finish the authentication process by attempting to obtain an access
// token.  If authorization was granted, the user will be logged in.
// Otherwise, authentication has failed.

server.get('/auth/provider/callback',
  passport.authenticate('provider', { successRedirect: '/',
                                      failureRedirect: '/login' }));
```

### Шаг 5. Добавление вспомогательного метода IsAuthenticated() в маршруты

```Javascript
// Simple route middleware to ensure user is authenticated.
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.

var ensureAuthenticated = function(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
};

```

### Шаг 6. Добавление механизма кэширования для файлов cookie

```Javascript
// Passport session setup.
//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});
```
### Шаг 7. Защита некоторых конечных точек

Для защиты конечных точек необходимо указать вызов passport.authenticate() с протоколом, который вы хотите использовать.

Изменим наш маршрут в коде сервера, чтобы сделать нечто более интересное:

```Javascript
server.get('/tasks', passport.authenticate('provider', { session: false }), listTasks);
```


## Шаг 19. Снова запустите свой сервер приложения и убедитесь, что он вас отклоняет.

Снова используем `curl`, чтобы определить, если у нас сейчас защита OAuth2 применительно к нашим конечным точкам. Мы это сделаем до запуска любого клиентского пакета SDK для этой конечной точки. Возвращаемые заголовки должны содержать достаточные сведения, чтобы мы поняли, что двигаемся в правильном направлении.

Сначала убедитесь, выполняется ли ваш экземпляр monogoDB.

	$sudo mongod

Затем перейдите в каталог и запустите curling.

	$ cd azuread
	$ node server.js

Повторите базовую команду GET:

	$ curl -isS http://127.0.0.1:8888/tasks/
	HTTP/1.1 302 Moved Temporarily
	Connection: close
	Location: https://login.windows.net/468a75f4-f9a7-4dc4-a527-4f4522734790/oauth2/authorize?response_type=code&redirect_uri=&client_id=123
	Content-Length: 0
	Date: Tue, 04 Feb 2014 02:15:14 GMT


302 — это искомый ответ, так как он указывает, что уровень Passport пытается перенаправлять к конечной точке проверки подлинности, а это именно то, что нам нужно.

## Поздравляем! Теперь у вас есть служба REST API, в которой применяется OAuth2.

До сих пор вы успешно применяли этот сервер без использования клиента, совместимого с OAuth2. Теперь вам будет нужно поработать с дополнительным пошаговым руководством.

Если вы просто искали сведения о реализации интерфейса REST API с использованием Restify и OAuth2, то у вас уже есть нужный код, чтобы продолжать разработку своей службы и применять приведенный пример.

Если вам интересно узнать о следующих шагах по изучению ADAL, то ниже вы найдете список некоторых поддерживаемых клиентов ADAL, которые мы рекомендуем использовать:

Просто клонируйте их на своем компьютере разработчика и настройте нужным образом, как указано в этом пошаговом руководстве.

[ADAL для iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL для Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[ADAL для .Net](http://msdn.microsoft.com/library/windowsazure/jj573266.aspx)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]
 

<!---HONumber=Oct15_HO3-->
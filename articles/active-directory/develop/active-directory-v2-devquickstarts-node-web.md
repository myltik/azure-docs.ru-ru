---
title: "Вход в веб-приложение Node.js версии 2.0 в Azure Active Directory | Документация Майкрософт"
description: "Сведения о создании веб-приложения Node js, которое поддерживает вход пользователей в систему с помощью личной учетной записи Майкрософт, а также рабочей или учебной учетной записи."
services: active-directory
documentationcenter: nodejs
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 1b889e72-f5c3-464a-af57-79abf5e2e147
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: f7479260c7c2e10f242b6d8e77170d4abe8634ac
ms.openlocfilehash: 6d49c742f72440e22830915c90de009d9188db2a
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017

---
# <a name="add-sign-in-to-a-nodejs-web-app"></a>Добавление функции входа в веб-приложение Node.js

> [!NOTE]
> Не все сценарии и компоненты Azure Active Directory поддерживают конечную точку версии 2.0. Чтобы определить, стоит ли вам использовать конечную точку версии 2.0 или 1.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).
> 

В рамках этого руководства мы используем Passport, чтобы выполнить следующие задачи:

* Вход пользователей в веб-приложение с помощью Azure Active Directory (Azure AD) и конечной точки версии 2.0.
* Отображение информации о пользователе.
* Выход пользователя из приложения.

**Passport** — промежуточный слой проверки подлинности для Node.js. Он отличается гибкой модульной структурой, которая позволяет сравнительно незаметно размещать его в любом приложении на основе Express или веб-приложении Restify. В Passport полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Azure AD. В этой статье описывается установка модуля и последующее добавление подключаемого модуля `passport-azure-ad` Azure AD.

## <a name="download"></a>Загрузить
Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Для выполнения действий в этом руководстве вы можете [скачать заготовку приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) или клонировать структуру:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Вы также можете воспользоваться готовым приложением в конце этого руководства.

## <a name="1-register-an-app"></a>Шаг 1. Регистрация приложения
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) или выполните [эти подробные указания](active-directory-v2-app-registration.md), чтобы зарегистрировать приложение. Не забудьте выполнить следующие действия.

* Скопируйте **идентификатор приложения**, назначенный приложению. Он потребуется для работы с этим руководством.
* Добавьте **веб-платформу** для своего приложения.
* Скопируйте значение **URI перенаправления** с портала. Необходимо использовать значение URI по умолчанию — `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-add-prerequisities-to-your-directory"></a>Шаг 2. Добавление необходимых компонентов в ваш каталог
В окне командной строки замените каталоги, чтобы перейти к корневой папке, если вы еще этого не сделали. Выполните следующие команды:

* `npm install express`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install restify`
* `npm install mongoose`
* `npm install bunyan`
* `npm install assert-plus`
* `npm install passport`
* `npm install webfinger`
* `npm install body-parser`
* `npm install express-session`
* `npm install cookie-parser`

Кроме этого, в схему быстрого запуска мы включили `passport-azure-ad`.

* `npm install passport-azure-ad`

Эта команда устанавливает библиотеки, используемые `passport-azure-ad`.

## <a name="3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Шаг 3. Настройка в приложении использования стратегии Passport-Node.js
Настройте промежуточный слой Express, чтобы использовать протокол проверки подлинности OpenID Connect. Passport также будет использоваться для выдачи запросов входа и выхода, управления сеансом пользователя и получения сведений о пользователе.

1.  Откройте файл config.js в корневой папке проекта. В разделе `exports.creds` введите значения конфигурации приложения.
  
  * `clientID`: **идентификатор приложения**, который портал Azure назначил вашему приложению.
  * `returnURL`: **универсальный код ресурса (URI) перенаправления**, который вы указали на портале.
  * `clientSecret`: секрет, созданный на портале.

2.  Откройте файл app.js в корневой папке проекта. Чтобы вызвать стратегию OIDCStrategy, поставляемую с `passport-azure-ad`, добавьте следующий вызов:

  ```JavaScript
  var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

  // Add some logging
  var log = bunyan.createLogger({
      name: 'Microsoft OIDC Example Web Application'
  });
  ```

3.  Чтобы обработать запросы на вход, используйте указанную стратегию:

  ```JavaScript
  // Use the OIDCStrategy within Passport (section 2)
  //
  //   Strategies in Passport require a `validate` function. The function accepts
  //   credentials (in this case, an OpenID identifier), and invokes a callback
  //   with a user object.
  passport.use( new OIDCStrategy({
      callbackURL: config.creds.returnURL,
      realm: config.creds.realm,
      clientID: config.creds.clientID,
      clientSecret: config.creds.clientSecret,
      oidcIssuer: config.creds.issuer,
      identityMetadata: config.creds.identityMetadata,
      responseType: config.creds.responseType,
      responseMode: config.creds.responseMode,
      skipUserProfile: config.creds.skipUserProfile
      scope: config.creds.scope
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
      log.info('Example: Email address we received was: ', profile.email);
      // Asynchronous verification, for effect...
      process.nextTick(function () {
        findByEmail(profile.email, function(err, user) {
          if (err) {
            return done(err);
          }
          if (!user) {
            // "Auto-registration"
            users.push(profile);
            return done(null, profile);
          }
          return done(null, user);
        });
      });
    }
  ));
  ```

Для Passport свойственно аналогичное поведение для всех стратегий (Twitter, Facebook и т. д.). Все авторы стратегии придерживаются этого шаблона. Передайте стратегию `function()`, которая использует маркер и значение `done` в качестве параметров. Стратегия возвращается после того, как выполнит всю свою работу. Сохраните данные пользователя и маркер, чтобы не задавать их в следующий раз повторно.

  > [!IMPORTANT]
  > Приведенный выше код принимает любого пользователя, который может пройти проверку подлинности на сервере. Это называется автоматической регистрацией. На рабочих серверах не нужно разрешать другим пользователям выполнять операции входа без прохождения процесса регистрации, который вы сочтете приемлемым. Это обычное поведение в приложениях для потребителей. Приложение можно зарегистрировать в Facebook, но потребуется ввести дополнительные сведения. Если в этом руководстве вы не использовали программу командной строки, сообщение электронной почты можно извлечь из возвращаемого объекта маркера. Затем вы можете попросить пользователя ввести дополнительные сведения. Так как это всего лишь тестовый сервер, просто добавьте пользователей в базу данных в памяти.
  > 
  > 

4.  Добавьте методы, которые позволяют отслеживать пользователей, выполнивших вход, в соответствии с требованиями Passport. Сюда относится сериализация и десериализация информации о пользователе:

  ```JavaScript

  // Passport session setup (section 2)

  //   To support persistent login sessions, Passport needs to be able to
  //   serialize users into, and deserialize users out of, the session. Typically,
  //   this is as simple as storing the user ID when serializing, and finding
  //   the user by ID when deserializing.
  passport.serializeUser(function(user, done) {
    done(null, user.email);
  });

  passport.deserializeUser(function(id, done) {
    findByEmail(id, function (err, user) {
      done(err, user);
    });
  });

  // Array to hold signed-in users
  var users = [];

  var findByEmail = function(email, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
      var user = users[i];
      log.info('we are using user: ', user);
      if (user.email === email) {
        return fn(null, user);
      }
    }
    return fn(null, null);
  };
  ```

5.  Добавьте код для загрузки ядра Express. Здесь мы используем шаблон по умолчанию /views и /routes, предоставляемый Express.

  ```JavaScript

  // Set up Express (section 2)

  var app = express();

  app.configure(function() {
    app.set('views', __dirname + '/views');
    app.set('view engine', 'ejs');
    app.use(express.logger());
    app.use(express.methodOverride());
    app.use(cookieParser());
    app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
    app.use(bodyParser.urlencoded({ extended : true }));
    // Initialize Passport!  Also use passport.session() middleware, to support
    // persistent login sessions (recommended).
    app.use(passport.initialize());
    app.use(passport.session());
    app.use(app.router);
    app.use(express.static(__dirname + '/../../public'));
  });

  ```

6.  Добавьте маршруты POST, которые будут передавать фактические запросы на вход в ядро `passport-azure-ad`:

  ```JavaScript

  // Auth routes (section 3)

  // GET /auth/openid
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. The first step in OpenID authentication involves redirecting
  //   the user to the user's OpenID provider. After authenticating, the OpenID
  //   provider redirects the user back to this application at
  //   /auth/openid/return.

  app.get('/auth/openid',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Authentication was called in the sample');
      res.redirect('/');
    });

  // GET /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called.
  //   In this example, it redirects the user to the home page.
  app.get('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });

  // POST /auth/openid/return
  //   Use passport.authenticate() as route middleware to authenticate the
  //   request. If authentication fails, the user is redirected back to the
  //   sign-in page. Otherwise, the primary route function is called. 
  //   In this example, it redirects the user to the home page.

  app.post('/auth/openid/return',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {

      res.redirect('/');
    });
  ```

## <a name="4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Шаг 4. Использование Passport для выдачи запросов на вход и выход в Azure AD
Теперь приложение правильно настроено для взаимодействия с конечной точкой версии 2.0 с использованием протокола проверки подлинности OpenID Connect. Стратегия `passport-azure-ad` берет на себя создание сообщений проверки подлинности, проверку маркеров из Azure AD и поддержку сеансов пользователя. Осталось предоставить пользователям возможности входа и выхода, а также собрать дополнительную информацию о вошедших в систему пользователях.

1.  Добавьте в файл app.js метод **по умолчанию**, а также методы **входа**, **учетной записи** и **выхода**.

  ```JavaScript

  //Routes (section 4)

  app.get('/', function(req, res){
    res.render('index', { user: req.user });
  });

  app.get('/account', ensureAuthenticated, function(req, res){
    res.render('account', { user: req.user });
  });

  app.get('/login',
    passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
    function(req, res) {
      log.info('Login was called in the sample');
      res.redirect('/');
  });

  app.get('/logout', function(req, res){
    req.logout();
    res.redirect('/');
  });

  ```

  Дополнительные сведения:
    
    * Маршрут `/` выполняет перенаправление в представление index.ejs. Он передает сведения о пользователе в запросе (если он существует).
    * Маршрут `/account` сначала *позволяет пройти проверку подлинности* (реализация выполняется в примере кода ниже). Затем он передает сведения о пользователе в запросе. Таким образом вы можете получить больше сведений о пользователе.
    * Маршрут `/login` вызывает структуру проверки подлинности `azuread-openidconnect` из `passport-azuread`. Если вызов завершается ошибкой, пользователь перенаправляется обратно на страницу `/login`.
    * Маршрут `/logout` вызывает представление logout.ejs (и маршрут). Это приводит к очистке файлов cookie, а затем он перенаправляет пользователя обратно к представлению index.ejs.

2.  Добавьте метод **EnsureAuthenticated**, использованный ранее в `/account`:

  ```JavaScript

  // Route middleware to ensure the user is authenticated (section 4)

  //   Use this route middleware on any resource that needs to be protected. If
  //   the request is authenticated (typically via a persistent login session),
  //   the request proceeds. Otherwise, the user is redirected to the
  //   sign-in page.
  function ensureAuthenticated(req, res, next) {
    if (req.isAuthenticated()) { return next(); }
    res.redirect('/login')
  }

  ```

3.  В файле app.js создайте сервер:

  ```JavaScript

  app.listen(3000);

  ```


## <a name="5-create-the-views-and-routes-in-express-that-you-show-your-user-on-the-website"></a>Шаг 5. Создание представлений и маршрутов в Express для отображения пользователя на веб-сайте
Добавьте маршруты и представления, отображающие сведения для пользователя. Маршруты и представления также обрабатывают созданные маршруты `/logout` и `/login`.

1. Создайте маршрут `/routes/index.js` в корневом каталоге.

  ```JavaScript

  /*
  * GET home page.
  */

  exports.index = function(req, res){
    res.render('index', { title: 'Express' });
  };
  ```

2.  Создайте маршрут `/routes/user.js` в корневом каталоге.

  ```JavaScript

  /*
  * GET users listing.
  */

  exports.list = function(req, res){
    res.send("respond with a resource");
  };
  ```

  Маршруты `/routes/index.js` и `/routes/user.js` являются простыми маршрутами, которые передают запрос в представления, включая пользователя, если он существует.

3.  Создайте представление `/views/index.ejs` в корневом каталоге. Эта страница вызывает методы **входа** и **выхода**. Вам также необходимо использовать представление `/views/index.ejs`, чтобы записать сведения об учетной записи. Вы можете использовать условное выражение `if (!user)`, так как сведения о пользователе, передаваемые в запросе, свидетельствуют о том, что он вошел в систему.

  ```JavaScript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
      <h2>Hello, <%= user.displayName %>.</h2>
      <a href="/account">Account info</a></br>
      <a href="/logout">Sign out</a>
  <% } %>
  ```

4.  Создайте представление `/views/account.ejs` в корневом каталоге. Представление `/views/account.ejs` позволяет просмотреть дополнительные сведения, которые `passport-azuread` помещает в запрос пользователя.

  ```Javascript
  <% if (!user) { %>
      <h2>Welcome! Please sign in.</h2>
      <a href="/login">Sign in</a>
  <% } else { %>
  <p>displayName: <%= user.displayName %></p>
  <p>givenName: <%= user.name.givenName %></p>
  <p>familyName: <%= user.name.familyName %></p>
  <p>UPN: <%= user._json.upn %></p>
  <p>Profile ID: <%= user.id %></p>
  <p>Full Claimes</p>
  <%- JSON.stringify(user) %>
  <p></p>
  <a href="/logout">Sign out</a>
  <% } %>
  ```

5.  Добавьте макет. Создайте представление `/views/layout.ejs` в корневом каталоге.

  ```HTML

  <!DOCTYPE html>
  <html>
      <head>
          <title>Passport-OpenID Example</title>
      </head>
      <body>
          <% if (!user) { %>
              <p>
              <a href="/">Home</a> |
              <a href="/login">Sign in</a>
              </p>
          <% } else { %>
              <p>
              <a href="/">Home</a> |
              <a href="/account">Account</a> |
              <a href="/logout">Sign out</a>
              </p>
          <% } %>
          <%- body %>
      </body>
  </html>
  ```

6.  Чтобы создать и запустить приложение, запустите `node app.js`. Затем перейдите сюда: `http://localhost:3000`.

7.  Войдите с помощью личной учетной записи Майкрософт либо рабочей или учебной учетной записи. Обратите внимание, что удостоверение пользователя отображается в списке /account. 

Теперь у вас есть веб-приложение, защищенное с помощью стандартных протоколов. Вы можете выполнить проверку подлинности пользователей в приложении с помощью личных, рабочих или учебных учетных записей.

## <a name="next-steps"></a>Дальнейшие действия
Полный пример (без ваших значений конфигурации) можно загрузить в виде [ZIP-архива](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Теперь можно перейти к более сложным темам. Вы можете попробовать следующее:

[Защита веб-API с помощью Node.js](active-directory-v2-devquickstarts-node-api.md)

Ниже приведены некоторые дополнительные ресурсы.

* [Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении](active-directory-appmodel-v2-overview.md)
* [Тег StackOverflow "azure-active-directory"](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Получение обновлений системы безопасности для наших продуктов
Мы рекомендуем вам зарегистрироваться для получения уведомлений о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на странице [технического центра безопасности](https://technet.microsoft.com/security/dd252948).



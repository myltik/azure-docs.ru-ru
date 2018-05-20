---
title: Начало работы с веб-приложением Node.js для Azure AD | Документация Майкрософт
description: Узнайте, как создать веб-приложение Node.js Express MVC, которое интегрируется с Azure AD для входа.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/20/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8068e193ac267ef23bdaab1fc23bc7bf9b0d330b
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Начало работы с веб-приложением Node.js для Azure AD
Мы выполним следующие действия с использованием Passport.

* Вход пользователя в приложение с помощью Azure Active Directory (Azure AD).
* Отображение информации о пользователе.
* Выход пользователя из приложения.

Passport — это промежуточный слой аутентификации для Node.js. Он отличается гибкой модульной структурой, которая позволяет сравнительно незаметно размещать его в любом приложении на основе Express или веб-приложении Restify. Он позволяет использовать разные стратегии с поддержкой аутентификации с помощью имени пользователя и пароля, учетных записей в Facebook и Twitter, и пр. Мы разработали стратегию для Microsoft Azure Active Directory. Теперь мы установим этот модуль и добавим подключаемый модуль Microsoft Azure Active Directory `passport-azure-ad`.

Для этого вам нужно:

1. зарегистрировать приложение;
2. настроить в нем использование стратегии `passport-azure-ad`;
3. использовать Passport для выдачи запросов на вход и выход в Azure AD.
4. вывести данные о пользователе.

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Вы можете [скачать заготовку приложения как ZIP-файл](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) или клонировать структуру:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Готовое приложение также приводится в конце этого руководства.

## <a name="step-1-register-an-app"></a>Шаг 1. Регистрация приложения
1. Войдите на [портале Azure](https://portal.azure.com).

2. В меню в верхней части страницы выберите учетную запись. В списке **Каталог** выберите клиент Active Directory для регистрации приложения.

3. Выберите **Все службы** в меню слева, а затем щелкните **Azure Active Directory**.

4. Щелкните **Регистрация приложений**, а затем выберите **Добавить**.

5. Следуйте инструкциям на экране, чтобы создать новое **веб-приложение** и (или) **веб-интерфейс API**.
  * **Имя** приложения является его описанием для пользователей.

  * **URL-адрес входа** — это базовый URL-адрес вашего приложения. Схема по умолчанию — `http://localhost:3000/auth/openid/return`.

6. Когда вы закончите регистрацию, Azure AD назначит приложению уникальный идентификатор. Это значение вам понадобится в следующих разделах, поэтому обязательно скопируйте его на странице приложения.
7. На странице **Параметры** -> **Свойства** приложения обновите его универсальный код ресурса (URI) идентификатора. **URI идентификатора приложения** — это уникальный идентификатор вашего приложения. Существует негласная договоренность использовать формат `https://<tenant-domain>/<app-name>`, например: `https://contoso.onmicrosoft.com/my-first-aad-app`.

## <a name="step-2-add-prerequisites-to-your-directory"></a>Шаг 2. Добавление предварительных требований в каталог
1. В командной строке перейдите в корневую папку (если вы еще не там) и выполните следующие команды:

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. Кроме того, вам потребуется `passport-azure-ad`:
    * `npm install passport-azure-ad`

Эта команда устанавливает библиотеки, от которых зависит `passport-azure-ad`.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>Шаг 3. Настройка приложения для использования стратегии passport-node-js
Здесь мы настроим Express для использования протокола аутентификации OpenID Connect. Passport позволяет выполнять разные действия, в том числе выдавать запросы на вход и выход, управлять сеансами пользователя и получать сведения о пользователе.

1. Сначала откройте файл `config.js` из корневого каталога проекта, а затем укажите параметры конфигурации приложения в разделе `exports.creds`.

  * `clientID` — это **идентификатор приложения**, присвоенный приложению на портале регистрации.

  * `returnURL` — это **универсальный код ресурса (URI) перенаправления**, который вы указали на портале.

  * `clientSecret` — это секрет, созданный на портале.

2. Теперь откройте файл `app.js` в корневой папке проекта. Добавьте следующий вызов, чтобы использовать стратегию `OIDCStrategy`, которая предоставляется с `passport-azure-ad`.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. Теперь вы можете использовать стратегию, на которую мы только что сослались, чтобы обрабатывать запросы на вход.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
        // asynchronous verification, for effect...
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
Для Passport свойственно аналогичное поведение для всех стратегий (Twitter, Facebook и т. д.); это поведение учитывают все разработчики модулей стратегий. В коде стратегии видно, что мы передаем ей функцию с параметрами token и done. Стратегия будет возвращена, когда завершит свою работу. Теперь нам нужно сохранить пользователя и обработать маркер, чтобы не запрашивать его повторно.

> [!IMPORTANT]
Приведенный выше код обрабатывает данные о любом пользователе, который выполняет аутентификацию на сервере. Это называется автоматической регистрацией. На рабочих серверах мы не рекомендуем разрешать аутентификацию без прохождения определенного процесса регистрации, который вы сочтете приемлемым. Это типично для клиентских приложений, в которых разрешается регистрация через Facebook, но затем следуют запросы на предоставление дополнительной информации. Если бы это не был пример приложения, мы могли бы извлечь электронный адрес пользователя из возвращенного объекта маркера и затем запросить у пользователя дополнительную информацию. Так как это всего лишь тестовый сервер, мы добавим пользователей в базу данных в памяти.


4. Далее мы добавим методы, которые позволят отслеживать вошедших пользователей, как того требует Passport. Сюда входят методы сериализации и десериализации информации о пользователе.

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
                done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
                findByEmail(id, function (err, user) {
                    done(err, user);
                });
            });

            // array to hold signed-in users
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

5. Далее добавим код для загрузки подсистемы Express. Здесь мы используем шаблон по умолчанию /views и /routes, предоставляемый Express.

    ```JavaScript

        // configure Express (section 2)

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

6. Наконец, добавьте маршруты, которые будут передавать фактические запросы на вход, в подсистему `passport-azure-ad`:

    ```JavaScript

        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

        // GET /auth/openid/return
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. If authentication fails, the user is redirected back to the
        //   sign-in page. Otherwise, the primary route function is called,
        //   which, in this example, redirects the user to the home page.
        app.get('/auth/openid/return',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('We received a return from AzureAD.');
            res.redirect('/');
          });

        // POST /auth/openid/return
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. If authentication fails, the user is redirected back to the
        //   sign-in page. Otherwise, the primary route function is called,
        //   which, in this example, redirects the user to the home page.
        app.post('/auth/openid/return',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('We received a return from AzureAD.');
            res.redirect('/');
          });
     ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Шаг 4. Использование Passport для выдачи запросов на вход и выход в Azure AD
Теперь приложение правильно настроено для взаимодействия с конечной точкой с использованием протокола проверки подлинности OpenID Connect. `passport-azure-ad` берет на себя создание сообщений проверки подлинности, проверку токенов из Azure AD и поддержку сеансов пользователя. Осталось предоставить пользователям возможности входа и выхода, а также собрать дополнительную информацию о вошедших в систему пользователях.

1. Сначала нужно добавить в файл `app.js` метод по умолчанию, а также методы входа, учетной записи и выхода.

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
            log.info('Login was called in the Sample');
            res.redirect('/');
        });

        app.get('/logout', function(req, res){
          req.logout();
          res.redirect('/');
        });

    ```

2. Рассмотрим это подробно.

  * Маршрут `/` выполняет перенаправление в представление index.ejs, передавая пользователя в запросе (если он существует).
  * Маршрут `/account` сначала *проверяет, выполнена ли аутентификация* (это мы реализуем в следующем примере), а затем передает пользователя в запросе. Так мы можем получить дополнительные сведения о пользователе.
  * Маршрут `/login` вызывает наше средство аутентификации azuread-openidconnect из `passport-azuread`. Если вызов завершается ошибкой, пользователь перенаправляется обратно на страницу /login.
  * Маршрут `/logout` просто вызывает logout.ejs (и соответствующий маршрут), который очищает файлы cookie и возвращает пользователя к index.ejs.

3. В конец файла `app.js` мы добавляем метод **EnsureAuthenticated**, который используется в `/account`, как показано выше.

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. Наконец, давайте создадим сам сервер в `app.js`.

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>Шаг 5. Создание представлений и маршрутов в Express для отображения пользователя на веб-сайте
Теперь файл `app.js` готов. Нам осталось только добавить маршруты и представления, которые отобразят для пользователя получаемую информацию, а также обработают созданные нами маршруты `/logout` и `/login`.

1. Создайте маршрут `/routes/index.js` в корневом каталоге.

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. Создайте маршрут `/routes/user.js` в корневом каталоге.

    ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
    ```

 Эти маршруты передают запрос в наши представления, включая пользователя, если он имеется.

3. Создайте представление `/views/index.ejs` в корневом каталоге. Это простая страница, которая вызывает наши методы входа и выхода и позволяет получить информацию об учетной записи. Обратите внимание, что вы можете использовать условное выражение `if (!user)`, так как сведения о пользователе, передаваемые в запросе, свидетельствуют о том, что в систему вошел пользователь.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. Создайте представление `/views/account.ejs` в корневом каталоге, чтобы мы могли просматривать дополнительную информацию, помещенную `passport-azure-ad` в запрос пользователя.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. Теперь давайте все красиво оформим, добавив макет. Создайте представление /views/layout.ejs в корневом каталоге.

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
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

## <a name="next-steps"></a>Дополнительная информация
Наконец, создайте и запустите приложение. Выполните `node app.js` и откройте в браузере страницу `http://localhost:3000`.

Выполните вход с личной, рабочей или учебной учетной записью Майкрософт. Проверьте, как удостоверение пользователя отображается в списке /account. Теперь у вас есть веб-приложение, защищенное с помощью стандартных отраслевых протоколов, которое может выполнять аутентификацию пользователей с использованием личных, рабочих и учебных учетных записей.

Полный пример (без ваших значений конфигурации) можно загрузить в виде [ZIP-архива](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Вы также можете клонировать его из репозитория GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

Теперь можно перейти к более сложным темам. Вы можете попробовать следующее:

[Приступая к работе с веб-интерфейсом API для узла](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

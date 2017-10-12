---
title: "Добавление входа в веб-приложение Node.js для Azure B2C | Документация Майкрософт"
description: "Как создать веб-приложение Node.js, позволяющее пользователям входить с помощью клиента B2C."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: db97f84a-1f24-447b-b6d2-0265c6896b27
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: hero-article
ms.date: 03/10/2017
ms.author: xerners
ms.openlocfilehash: c85b8f8434d1e837ac96ac63b9b37f990677ed6e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: добавление входа в веб-приложение Node.js

**Passport** — промежуточный слой проверки подлинности для Node.js. Гибкая модульная структура Passport позволяет незаметно устанавливать его в любом приложении на основе Express или в веб-приложении Restify. Полный набор стратегий поддерживает проверку подлинности с помощью имени пользователя и пароля, Facebook, Twitter и других средств.

Мы разработали стратегию для Azure Active Directory (Azure AD). Сначала необходимо установить этот модуль, а затем добавить подключаемый модуль Azure AD `passport-azure-ad`.

Для этого необходимо выполнить следующие действия.

1. Зарегистрировать приложение с помощью Azure AD.
2. Настройте в приложении использование подключаемого модуля `passport-azure-ad`.
3. использовать Passport для выдачи запросов на вход и выход в Azure AD.
4. Ввести данные пользователя.

Код для этого руководства размещен на портале [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Вы можете [скачать схему приложения как ZIP-файл](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip). Ее также можно клонировать:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

Готовое приложение приводится в конце этого руководства.

## <a name="get-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент.  Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д. Прежде чем продолжать работу с руководством, [создайте каталог B2C](active-directory-b2c-get-started.md), если вы его еще не создали.

## <a name="create-an-application"></a>Создание приложения

Затем необходимо создать приложение в каталоге B2C. Таким образом в Azure AD поступят сведения, необходимые для безопасного взаимодействия с вашим приложением. И клиентское приложение, и веб-API будут представлены одним **идентификатором приложения**, так как они включают в себя одно приложение логики. Создайте приложение, выполнив [эти указания](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

- Включите в приложение **веб-приложение**/**веб-API**.
- Введите `http://localhost:3000/auth/openid/return` в качестве значения **URL-адреса ответа**. Это URL-адрес по умолчанию для данного примера кода.
- Создайте для своего приложения **секрет приложения** и скопируйте его. Оно понадобится вам позднее. Обратите внимание, что перед использованием это значение должно быть [экранировано для XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) .
- Скопируйте **идентификатор приложения** , назначенный приложению. Этот идентификатор также понадобится вам позднее.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Это приложение содержит три вида идентификации: регистрация, вход и вход с помощью учетной записи Facebook. Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). При создании трех политик обязательно выполните указанные ниже действия.

- В политике регистрации укажите **отображаемое имя** и другие атрибуты регистрации.
- Выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта** для каждой политики. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой созданной политики. У него должен быть префикс `b2c_1_`.  Эти имена политик понадобятся вам через некоторое время.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к сборке приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Дополнительные сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="add-prerequisites-to-your-directory"></a>Добавление предварительных требований в ваш каталог

В окне командной строки замените каталоги на корневую папку, если вы еще этого не сделали. Выполните следующие команды:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

Кроме того, в схему быстрого запуска мы включили раздел `passport-azure-ad` для предварительной версии.

- `npm install passport-azure-ad`

Будет выполнена установка библиотек, которые зависят от `passport-azure-ad`.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Настройка в приложении использования стратегии Passport-Node.js
Настройте в ПО промежуточного слоя Express использование протокола проверки подлинности OpenID Connect. Кроме всего прочего, Passport будет использоваться для выдачи запросов на вход и выход, управления сеансами пользователей и получения сведений о пользователях.

Откройте файл `config.js` в корне проекта и введите значения конфигурации приложения в разделе `exports.creds`.
- `clientID` — это **идентификатор приложения**, назначенный приложению на портале регистрации.
- `returnURL` — это **URI перенаправления**, который вы указали на портале.
- `tenantName` — это имя клиента вашего приложения, например **contoso.onmicrosoft.com**.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Откройте файл `app.js` в корневой папке проекта. Добавьте следующий вызов, чтобы вызвать стратегию `OIDCStrategy`, которая поставляется с `passport-azure-ad`.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Используйте стратегию, указанную для обработки запросов на вход.

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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
Passport использует аналогичный шаблон для всех своих стратегий (в том числе Twitter и Facebook). Все авторы стратегии придерживаются этого шаблона. Если взглянуть на стратегию, можно увидеть, что вы передаете ей `function()` с маркером и `done` в качестве параметра. Стратегия будет возвращена после выполнения всех задач. Сохраните данные пользователя и маркер, чтобы не задавать их в следующий раз повторно.

> [!IMPORTANT]
Приведенный выше код принимает всех пользователей, которые проходят проверку подлинности на сервере. Это называется автоматической регистрацией. Если вы используете рабочие серверы, вам не следует пропускать пользователей, если они не пройдут через установленный процесс регистрации. Этот шаблон часто используется в потребительских приложениях. Они позволяют регистрироваться с помощью Facebook, но затем появляется запрос на ввод дополнительных сведений. Если бы это не был пример приложения, мы могли бы извлечь электронный адрес из возвращенного объекта маркера, а затем запросить у пользователя дополнительную информацию. Так как это всего лишь тестовый сервер, мы просто добавим пользователей в базу данных в памяти.

Добавьте методы, которые позволяют отслеживать пользователей, выполнивших вход, в соответствии с требованиями Passport. Сюда относится сериализация и десериализация информации о пользователе:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Добавьте код для загрузки ядра Express. В следующем коде используется шаблон по умолчанию `/views` и `/routes`, предоставленный Express.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Добавьте маршруты `POST`, которые будут передавать фактические запросы на вход в ядро `passport-azure-ad`:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Использование Passport для выдачи запросов на вход и выход в Azure AD

Теперь приложение правильно настроено для взаимодействия с конечной точкой версии 2.0 с использованием протокола проверки подлинности OpenID Connect. `passport-azure-ad` берет на себя создание сообщений проверки подлинности, проверку маркеров из Azure AD и поддержку сеанса пользователя. Осталось предоставить пользователям возможности входа и выхода, а также собрать дополнительную информацию о вошедших в систему пользователях.

Сначала добавьте в файл `app.js` стандартные методы входа, учетной записи и выхода:

```JavaScript

//Routes (Section 4)

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

Подробное описание этих методов приводится здесь.
- Маршрут `/` выполнит перенаправление в представление `index.ejs`, передавая сведения о пользователе в запросе (если они есть).
- Маршрут `/account` сначала проверяет, прошли ли вы проверку подлинности (реализация показана ниже). Затем он передает сведения о пользователе в запросе, чтобы можно было получить дополнительные сведения о пользователе.
- Маршрут `/login` вызывает структуру проверки подлинности `azuread-openidconnect` из `passport-azure-ad`. Если не удается вызвать, маршрут перенаправляет пользователя обратно к `/login`.
- `/logout` просто вызывает `logout.ejs` (и соответствующий маршрут). Это приводит к очистке файлов cookie, а затем перенаправляет пользователя обратно к `index.ejs`.


В последней части файла `app.js` добавьте метод `EnsureAuthenticated`, используемый в маршруте `/account`.

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Наконец, создайте сам сервер в `app.js`.

```JavaScript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>Создание представлений и маршрутов в Express для вызова политик

`app.js` готово. Осталось добавить маршруты и представления, которые позволяют вызывать политики входа и регистрации. Они также обрабатывают созданные вами маршруты `/logout` и `/login`.

Создайте маршрут `/routes/index.js` в корневом каталоге.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Создайте маршрут `/routes/user.js` в корневом каталоге.

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Эти простые маршруты передают запросы представлениям. Они содержат данные пользователя, если он есть.

Создайте представление `/views/index.ejs` в корневом каталоге. Это простая страница, которая вызывает политики входа и выхода. Вы также можете использовать ее для получения сведений об учетной записи. Обратите внимание, что вы можете использовать условное значение `if (!user)`, так как данные о пользователе передаются в запросе в качестве доказательства того, что пользователь выполнил вход.

```JavaScript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

Создайте представление `/views/account.ejs` в корневом каталоге, чтобы вы могли просматривать дополнительную информацию, которую `passport-azure-ad` помещает в запрос пользователя.

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
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Выполните сборку и запустите приложение.

Запустите `node app.js` и перейдите на страницу `http://localhost:3000`.


Зарегистрируйтесь или войдите в приложение с помощью адреса электронной почты или учетной записи Facebook. Выйдите и снова войдите от имени другого пользователя.

##<a name="next-steps"></a>Дальнейшие действия

Полный пример (без ваших значений конфигурации) можно загрузить в виде [ZIP-архива](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Кроме того, его можно клонировать из GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Теперь можно перейти к более сложным темам. Попробуйте ознакомиться с такими материалами:

[Azure AD B2C: защита веб-API с помощью Node.js](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->

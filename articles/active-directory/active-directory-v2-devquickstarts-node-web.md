<properties
	pageTitle="Веб-приложение Node.js для Azure AD версии 2.0 | Microsoft Azure"
	description="Как создать приложение Node JS, которое поддерживает вход пользователей в систему с помощью лично, рабочей и учебной учетной записью Майкрософт."
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

# Добавление функции входа в веб-приложение Node.js


> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).


Здесь мы используем Passport для следующего:

- Вход пользователя в приложение с помощью Azure AD и конечной точки версии 2.0.
- Отображение некоторой информации о пользователе.
- Выход пользователя из приложения.

**Passport** — промежуточный слой проверки подлинности для Node.js. Он имеет очень гибкую и модульную структуру, которая позволяет сравнительно незаметно размещать данный слой в любом приложении на основе Express или в веб-приложении Resitify. Полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Microsoft Azure Active Directory. Мы установим этот модуль, а затем добавим подключаемый модуль Microsoft Azure Active Directory `passport-azure-ad`.

## Загрузить

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Готовое приложение также приводится в конце этого руководства.

## 1\. Регистрация приложения
Создайте приложение на странице [apps.dev.microsoft.com](https://apps.dev.microsoft.com) или выполните [эти подробные указания](active-directory-v2-app-registration.md). Не забудьте:

- запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.
- Добавьте **веб-платформу** для своего приложения.
- Введите правильный **универсальный код ресурса (URI) перенаправления**. Универсальный код ресурса (URI) перенаправления сообщает Azure AD, куда следует направлять ответы аутентификации. Значение по умолчанию в этом руководстве — `http://localhost:3000/auth/openid/return`.

## 2. Добавление предварительных требований в ваш каталог

В командной строке сделайте текущей корневую папку, если это еще не было сделано, и выполните следующие команды:

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

- Кроме того, в схему быстрого запуска мы включили `passport-azure-ad`.

- `npm install passport-azure-ad`


Будет выполнена установка библиотек, которые зависят от passport-azure-ad.

## 3\. Настройка приложения для использования стратегии passport-node-js
Здесь мы настроим промежуточный слой Express для использования протокола проверки подлинности OpenID Connect. Кроме всего прочего, Passport будет использоваться для выдачи запросов входа и выхода, управления сеансом пользователя и получения сведений о пользователе.

-	Сначала откройте файл `config.js` в корневом каталоге проекта, а затем укажите параметры конфигурации приложения в разделе `exports.creds`.
    -	`clientID:` — это **идентификатор приложения**, присвоенный приложению на портале регистрации.
    -	`returnURL` — это **универсальный код ресурса (URI) перенаправления**, который вы указали на портале.
    - `clientSecret` — это секретный код, созданный на портале

- Затем откройте в корневом каталоге проекта файл `app.js` и добавьте вызов стратегии `OIDCStrategy`, входящей в состав `passport-azure-ad`.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

- После этого используйте стратегию, которую мы только что использовали в ссылке, чтобы обрабатывать запросы на вход.

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2)
//
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
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
Passport использует аналогичную схему для всех своих стратегий (Twitter, Facebook и т. д.), представленных всеми модулями записи стратегий. Просматривая стратегию, можно увидеть, что мы передаем function(), у которой есть маркер, и обрабатываем параметры. Стратегия возвратится к нам после выполнения всей своей работы. После ее возврата потребуется сохранить пользователя и спрятать маркер, так как нам больше не придется его запрашивать.

> [AZURE.IMPORTANT]
Приведенный выше код принимает всех пользователей, которые прошли аутентификацию на сервере. Это называется автоматической регистрацией. На рабочих серверах не нужно разрешать другим пользователям входить без выбранной вами регистрации. Это типично для клиентских приложений, в которых разрешается регистрация через Facebook, но затем последуют запросы на дополнительную информацию. Если бы это не был пример приложения, мы могли бы извлечь электронный адрес из возвращенного объекта маркера и затем запросить дополнительную информацию. Так как это всего лишь тестовый сервер, мы просто добавим их в базу данных в памяти.

- Далее добавим методы, которые позволят отслеживать список вошедших пользователей согласно требованиям Passport. Сюда относится сериализация и десериализация информации о пользователе:

```JavaScript

// Passport session setup. (Section 2)

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

// array to hold logged in users
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

- Далее добавим код для загрузки ядра Express. Как вы видите, мы используем шаблон по умолчанию /views и /routes, предоставляемый Express.

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
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Наконец, мы добавим маршруты POST, которые будут передавать в ядро `passport-azure-ad` запросы на вход.

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## 4\. Использование Passport для выдачи запросов на вход и выход в Azure AD

Теперь в приложении настроена связь с конечной точкой версии 2.0 с использованием протокола проверки подлинности OpenID Connect. Подключаемый модуль `passport-azure-ad` сам создает сообщения проверки подлинности, проверяет токены из Azure AD и поддерживает сеанс пользователя. Осталось дать пользователям возможность входа, выхода и сбора дополнительной информации о вошедшем в систему пользователе.

- Сначала добавьте в файл `app.js` метод по умолчанию, а также методы входа, учетной записи и выхода.

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

-	Рассмотрим это подробно.
    -	Маршрут `/` выполнит перенаправление в представление index.ejs, передавая сведения о пользователе в запросе (если они есть).
    - Маршрут `/account` сначала ***проверит, выполнен ли вход *** (это мы реализуем ниже), а затем передаст сведения о пользователе в запросе. Таким образом мы сможем получить дополнительные сведения о пользователе.
    - Маршрут `/login` вызовет структуру проверки подлинности azuread-openidconnect из `passport-azuread` и, если проверка не будет пройдена, перенаправит пользователя назад к /login
    - `/logout` просто вызовет logout.ejs (и маршрут), который очищает файлы cookie, и вернет пользователя к index.ejs


- В конец файла `app.js` добавьте метод EnsureAuthenticated, который используется в `/account` выше.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- Наконец, создайте в `app.js` сам сервер.

```JavaScript

app.listen(3000);

```


## 5\. Создание представлений и маршрутов в Express для отображения пользователя на веб-сайте

Наш файл `app.js` готов. Теперь надо просто добавить маршруты и представления, которые отобразят получаемую информацию для пользователя, а также обработают созданные нами маршруты `/logout` и `/login`.

- Создайте маршрут `/routes/index.js` в корневом каталоге.

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Создание маршрута `/routes/user.js` в корневом каталоге

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Эти простые маршруты будут передавать запрос в наши представления, включая пользователя, если он имеется.

- Создайте представление `/views/index.ejs` в корневом каталоге. Это простая страница, которая будет вызывать наши методы входа и выхода и позволит получать информацию об учетной записи. Обратите внимание, что вы можете использовать условное выражение `if (!user)`, так как сведения о пользователе, передаваемые в запросе, свидетельствуют о том, что в систему вошел пользователь.

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

- Создайте представление `/views/account.ejs` в корневом каталоге, чтобы мы могли просматривать дополнительную информацию, помещенную `passport-azuread` в запрос пользователя.

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
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Наконец, давайте все красиво оформим, добавив макет. Создайте представление /views/layout.ejs в корневом каталоге.

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

Наконец, постройте и запустите свое приложение!

Запустите `node app.js` и перейдите на страницу `http://localhost:3000`.


Выполните вход с личной учетной записью Майкрософт, рабочей или учебной учетной записью и обратите внимание на то, как удостоверение пользователя отображается в списке /account. Теперь у вас есть веб-приложение, защищенное с помощью стандартных отраслевых протоколов, которое может проверять подлинность пользователей с помощью личных, рабочих и учебных учетных записей.

##Дальнейшие действия

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip) или клонировать с GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Теперь можно перейти к более сложным темам. Можно попробовать:

[Защита веб-API Node.js с помощью конечной точки версии 2.0](active-directory-v2-devquickstarts-node-api.md)

Дополнительные ресурсы:
- [Руководство разработчика версии 2.0 >>](active-directory-appmodel-v2-overview.md)
- [Тег StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

<!---HONumber=AcomDC_0921_2016-->
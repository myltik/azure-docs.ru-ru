<properties
	pageTitle="Приступая к работе с входом и выходом Azure AD с помощью node.js"
	description="Как создать веб-приложение Node.js Express MVC, которое интегрируется с Azure AD для входа."
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

# Вход и выход веб-приложения с использованием Azure AD


Здесь мы используем Passport для следующего:

- Вход пользователя в приложение с помощью Azure AD и модели приложения версии 2.0.
- Отображение некоторой информации о пользователе.
- Выход пользователя из приложения.

**Passport** — промежуточный слой проверки подлинности для Node.js. Он имеет очень гибкую и модульную структуру, которая позволяет сравнительно незаметно размещать данный слой в любом приложении на основе Express или в веб-приложении Resitify. Полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Microsoft Azure Active Directory. Мы установим этот модуль, а затем добавим подключаемый модуль Microsoft Azure Active Directory `passport-azure-ad`.

Для этого необходимо:

1. зарегистрировать приложение;
2. настроить приложение для использования стратегии Passport-azure-ad;
3. использовать Passport для выдачи запросов на вход и выход в Azure AD.
4. распечатать данные о пользователе.

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Готовое приложение также приводится в конце этого руководства.

## 1\. Регистрация приложения
- Выполните вход на портале управления Azure.
- В левой панели навигации нажмите **Active Directory**.
- Выберите клиент, в котором нужно зарегистрировать приложение.
- Перейдите во вкладку **Приложения** и нажмите кнопку Добавить в нижней панели.
- Следуйте инструкциям на экране, а затем создайте новое **веб-приложение и/или WebAPI**.
    - **Имя** приложения отображает его описание конечным пользователям.
    -	**URL-адрес входа** — это базовый URL-адрес вашего приложения. Схема по умолчанию — http://localhost:3000/auth/openid/return``.
    - **URI идентификатора приложения** — это уникальный идентификатор вашего приложения. Соглашение заключается в использовании `https://<tenant-domain>/<app-name>`, например, `https://contoso.onmicrosoft.com/my-first-aad-app`
- После завершения регистрации Azure AD присваивает приложению уникальный идентификатор клиента. Это значение понадобится в следующих разделах, поэтому его стоит скопировать из вкладки «Настройка».

## 2\. Добавление предварительных требований в ваш каталог

В командной строке сделайте текущей корневую папку, если это еще не было сделано, и выполните следующие команды:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- Кроме того, вам также потребуется `passport-azure-ad`:

- `npm install passport-azure-ad`

Будет выполнена установка библиотек, которые зависят от passport-azure-ad.

## 3\. Настройка приложения для использования стратегии passport-node-js
Здесь мы настроим промежуточный слой Express для использования протокола проверки подлинности OpenID Connect. Кроме всего прочего, Passport будет использоваться для выдачи запросов входа и выхода, управления сеансом пользователя и получения сведений о пользователе.

-	Для начала откройте файл `config.js` в корне проекта, а затем введите значения параметров конфигурации приложения в разделе `exports.creds`.
    -	`clientID:` — это **идентификатор приложения**, назначенный приложению на портале регистрации.
    -	`returnURL` — это **URI перенаправления**, который был введен на портале.
    - `clientSecret` — это секретный код, созданный на портале

- Затем откройте в корневом каталоге проекта файл `app.js` и добавьте вызов стратегии `OIDCStrategy`, входящей в состав `passport-azure-ad`.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// add a logger

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
Passport использует аналогичную схему для всех своих стратегий (Twitter, Facebook и т. д.), представленных всеми модулями записи стратегий. Просматривая стратегию, можно увидеть, что мы передаем function(), у которой есть маркер, и обрабатываем параметры. Стратегия возвратится к нам после выполнения всей своей работы. После ее возврата потребуется сохранить пользователя и спрятать маркер, так как нам больше не придется его запрашивать.


> [AZURE.IMPORTANT]Приведенный выше код принимает всех пользователей, которые прошли аутентификацию на сервере. Это называется автоматической регистрацией. На рабочих серверах не нужно разрешать другим пользователям входить без выбранной вами регистрации. Это типично для клиентских приложений, в которых разрешается регистрация через Facebook, но затем последуют запросы на дополнительную информацию. Если бы это не был пример приложения, мы могли бы извлечь электронный адрес из возвращенного объекта маркера и затем запросить дополнительную информацию. Так как это всего лишь тестовый сервер, мы просто добавим их в базу данных в памяти.

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

- Наконец, добавим маршруты, которые будут передавать действительные запросы на вход в ядро `passport-azure-ad`:

```JavaScript

// Our Auth routes (Section 3)

// POST /auth/openid
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
    log.info('We received a return from AzureAD.');
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
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## 4. Use Passport to issue sign-in and sign-out requests to Azure AD

Your app is now properly configured to communicate with the v2.0 endpoint using the OpenID Connect authentication protocol.  `passport-azure-ad` has taken care of all of the ugly details of crafting authentication messages, validating tokens from Azure AD, and maintaining user session.  All that remains is to give your users a way to sign in, sign out, and gather additional info on the logged in user.

- First, lets add the default, login, account, and logout methods to our `app.js` file:

```JavaScript

//Маршруты (раздел 4)

app.get('/', function(req, res){ res.render('index', { user: req.user }); });

app.get('/account', ensureAuthenticated, function(req, res){ res.render('account', { user: req.user }); });

app.get('/login', passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }), function(req, res) { log.info('Вход инициирован в примере'); res.redirect('/'); });

app.get('/logout', function(req, res){ req.logout(); res.redirect('/'); });

```

-	Let's review these in detail:
    -	The `/` route will redirect to the index.ejs view passing the user in the request (if it exists)
    - The `/account` route will first ***ensure we are authenticated*** (we implement that below) and then pass the user in the request so that we can get additional information about the user.
    - The `/login` route will call our azuread-openidconnect authenticator from `passport-azuread` and if that doesn't succeed will redirect the user back to /login
    - The `/logout` will simply call the logout.ejs (and route) which clears cookies and then return the user back to index.ejs


- For the last part of `app.js`, let's add the EnsureAuthenticated method that is used in `/account` above.

```JavaScript

// Простое ПО промежуточного слоя для маршрутизации, позволяющее пользователю пройти проверку подлинности. (Раздел 4)

// Используйте это ПО промежуточного слоя маршрутизации для всех ресурсов, которые необходимо защитить. Если // запрос пройдет проверку подлинности (обычно с помощью постоянного сеанса входа), // запрос будет продолжен. В противном случае, пользователь будет перенаправлен на // страницу входа. function ensureAuthenticated(req, res, next) { if (req.isAuthenticated()) { return next(); } res.redirect('/login') } ```

- Наконец, создайте в `app.js` сам сервер.

```JavaScript

app.listen(3000);

```


## 5\. Создание представлений и маршрутов в Express для отображения пользователя на веб-сайте

Наш файл `app.js` готов. Теперь надо просто добавить маршруты и представления, которые отобразят получаемую информацию для пользователя, а также обработают созданные нами маршруты `/logout` и `/login`.

- Создайте в корневом каталоге маршрут `/routes/index.js`.

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

- Создайте в корневом каталоге представление `/views/account.ejs`, чтобы мы могли просматривать дополнительную информацию, которую `passport-azuread` передает в запрос пользователя.

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

<!DOCTYPE html> <html> <head> <title>Passport-OpenID Example</title> </head> <body> <% if (!user) { %> <p> <a href="/">Домашняя страница</a> | <a href="/login">Вход</a> </p> <% } else { %> <p> <a href="/">Домашняя страница</a> | <a href="/account">Учетная запись</a> | <a href="/logout">Выход</a> </p> <% } %> <%- body %> </body> </html>```

Наконец, постройте и запустите свое приложение!

Запустите `node app.js` и перейдите на страницу `http://localhost:3000`.


Выполните вход с личной учетной записью Майкрософт, рабочей или учебной учетной записью и обратите внимание на то, как удостоверение пользователя отображается в списке /account. Теперь у вас есть веб-приложение, защищенное с помощью стандартных отраслевых протоколов, которое может проверять подлинность пользователей с помощью личных, рабочих и учебных учетных записей.

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip) или клонировать с портала GitHub.

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


Теперь можно перейти к более сложным темам. Можно попробовать:

[Защита веб-интерфейса API с помощью Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=Oct15_HO3-->
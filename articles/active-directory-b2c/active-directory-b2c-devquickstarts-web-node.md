<properties
	pageTitle="Добавление варианта входа в веб-приложение nodeJS для предварительной версии Azure B2C| Microsoft Azure"
	description="Как создать веб-приложение Node.js, позволяющее пользователям входить с помощью клиента B2C."
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
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="brandwe"/>

# Предварительная версия B2C: добавление варианта входа в веб-приложение NodeJS


[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	В этой статье не рассматривается реализация входа, регистрации и управления профилями с помощью Azure AD B2C. Она посвящена вызову веб-API после того, как пользователь прошел проверку подлинности. Прочитайте [руководство по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md), чтобы изучить основы Azure AD B2C.

**Passport** — промежуточный слой проверки подлинности для Node.js. Он имеет очень гибкую и модульную структуру, которая позволяет сравнительно незаметно размещать данный слой в любом приложении на основе Express или в веб-приложении Resitify. Полный набор стратегий поддерживает процесс проверки подлинности с помощью имени пользователя и пароля, Facebook, Twitter и проч. Мы разработали стратегию для Microsoft Azure Active Directory. Мы установим этот модуль, а затем добавим подключаемый модуль Microsoft Azure Active Directory `passport-azure-ad`.

Чтобы сделать это, необходимо:

1. зарегистрировать приложение в Azure AD;
2. Настроить приложение для использования подключаемого модуля azure-ad-passport Passport.
3. использовать Passport для выдачи запросов на вход и выход в Azure AD.
4. распечатать данные о пользователе.

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) или клонировать ее:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git
```

Готовое приложение также приводится в конце этого руководства.

> [AZURE.WARNING] 	Для предварительной версии B2C следует использовать тот же идентификатор клиента или идентификатор приложения и политики как для сервера задач веб-API, так и для клиента, который к нему подключается. Это верно для учебников по iOS и Android. Если ранее вы создали приложение в любом из этих руководств, используйте эти значения и не создавайте новые в дальнейшем.

## 1\. Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для всех пользователей, приложений, групп и т. д. Если каталог B2C еще не создан, [создайте его](active-directory-b2c-get-started.md).

## 2\. Создание приложения

Теперь необходимо создать приложение в каталоге B2C. Оно будет передавать в Azure AD сведения, необходимые для безопасного взаимодействия с вашим приложением. В этом случае как клиентское приложение, так и веб-API будут представлены одним **идентификатором приложения**, так как они включают в себя одно приложение логики. Создайте приложение, выполнив [эти указания](active-directory-b2c-app-registration.md). Не забудьте

- Включите в приложение **веб-приложение или веб-API**.
- Введите `http://localhost/TodoListService` как **URL-адрес ответа**. Это — URL-адрес по умолчанию для этого примера кода.
- Создайте для своего приложения **секрет приложения** и скопируйте его. Скоро он вам понадобится. Обратите внимание, что это значение должно быть [экранировано для XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) перед использованием.
- Скопируйте **идентификатор приложения**, назначенный приложению. Он также вам скоро понадобится.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Создание политик

В Azure AD B2C всякое взаимодействие с пользователем определяется [**политикой**](active-directory-b2c-reference-policies.md). Это приложение содержит три вида идентификации: регистрация, вход и вход с помощью учетной записи Facebook. Вам нужно создать по одной политике каждого типа, как описано в [справочнике по политикам](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). При создании трех политик обязательно сделайте следующее.

- В политике регистрации выберите **Отображаемое имя** и другие атрибуты регистрации.
- В каждой политике выберите утверждения приложения **Отображаемое имя** и **Идентификатор объекта**. Можно также выбрать другие утверждения.
- Скопируйте **имя** каждой политики после ее создания. У него должен быть префикс `b2c_1_`. Эти имена политик понадобятся вам через некоторое время.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Создав три политики, можно приступать к созданию приложения.

Обратите внимание, что в данной статье не рассматривается использование политик, которые вы только что создали. Дополнительные сведения о работе политик в Azure AD B2C см. в [руководстве по началу работы с веб-приложениями .NET](active-directory-b2c-devquickstarts-web-dotnet.md).



## 4\. Добавление предварительных требований в ваш каталог

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

- Кроме того, в схему шаблона быстрого запуска мы включили специальный раздел `passport-azure-ad` для предварительной версии.

- `npm install passport-azure-ad`


Будет выполнена установка библиотек, которые зависят от passport-azure-ad.

## 5\. Настройка приложения для использования стратегии passport-node-js
Здесь мы настроим промежуточный слой Express для использования протокола проверки подлинности OpenID Connect. Кроме всего прочего, Passport будет использоваться для выдачи запросов входа и выхода, управления сеансом пользователя и получения сведений о пользователе.

-	Сначала откройте файл `config.js` в корневом каталоге проекта, а затем укажите параметры конфигурации приложения в разделе `exports.creds`.
    -	`clientID:` — это **идентификатор приложения**, присвоенный приложению на портале регистрации.
    -	`returnURL` — это **универсальный код ресурса (URI) перенаправления**, который вы указали на портале.
    - `tenantName:` — это **имя клиента** вашего приложения, например contoso.onmicrosoft.com.

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

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


## 5\. Создание представлений и маршрутов в Express для вызова политик

Наш файл `app.js` готов. Теперь надо просто добавить маршруты и представления, которые позволят нам вызывать политики входа и регистрации, а также обработают созданные нами маршруты `/logout` и `/login`.

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

- Создайте в корневом каталоге представление `/views/index.ejs`. Это простая страница, которая будет вызывать политики входа и выхода и позволит получать информацию об учетной записи. Обратите внимание, что вы можете использовать условное выражение `if (!user)`, так как сведения о пользователе, передаваемые в запросе, свидетельствуют о том, что в систему вошел пользователь.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login/?p=your facebook policy">Sign In with Facebook</a>
	<a href="/login/?p=your email sign-in policy">Sign In With Email</a>
	<a href="/login/?p=your email sign-up policy">Sign Up With Email</a>
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

Наконец, постройте и запустите свое приложение!

Запустите `node app.js` и перейдите на страницу `http://localhost:3000`.


Зарегистрируйтесь или войдите в приложение с помощью адреса электронной почты или учетной записи Facebook. Выйдите и снова войдите в другую учетную запись.



##Дальнейшие действия

Готовый пример (без ваших значений конфигурации) [можно скачать в виде ZIP-файла здесь](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip) или клонировать с GitHub.

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git
```

Теперь можно перейти к более сложным темам. Можно попробовать:

[Защита веб-API с помощью модели B2C в Node.js >>](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=AcomDC_0224_2016-->
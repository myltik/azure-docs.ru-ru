<properties
	pageTitle="Модель приложений версии 2.0 | Microsoft Azure"
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
	ms.date="08/25/2015"
	ms.author="brandwe"/>

# Предварительная версия модели приложений 2.0: реализация входа в веб-приложении NodeJS


  >[AZURE.NOTE]Эти сведения относятся к общедоступной предварительной версии модели приложений 2.0. Указания по интеграции с общедоступной службой Azure AD см. в статье [Руководство разработчика Azure Active Directory](active-directory-developers-guide.md).


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

Код в этом учебнике размещен на портале [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs). Для понимания процесса можно [скачать основу приложения как ZIP-файл](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) или клонировать ее:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

The completed application is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Web** platform for your app.
- Enter the correct **Redirect URI**. The redirect URI indicates to Azure AD where authentication responses should be directed - the default for this tutorial is `http://localhost:3000/auth/openid/return`.

## 2. Add pre-requisities to your directory

From the command-line, change directories to your root folder if not already there and run the following commands:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- In addition, we've included a special `passport-azure-ad` for our Preview in the skeleton of the quickstart. You'll need to install the pre-requisites in there as well.

- `cd /lib/passport-azure-ad`
- `npm install`

This will install the libraries that passport-azure-ad depend on.

## 3. Set up your app to use the passport-node-js strategy
Here, we'll configure the Express middleware to use the OpenID Connect authentication protocol.  Passport will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `config.js` file in the root of the project, and enter your app's configuration values in the `exports.creds` section.
    -	The `clientID:` is the **Application Id** assigned to your app in the registration portal.
    -	The `returnURL` is the **Redirect URI** you entered in the portal.
    - The `clientSecret` is the secret you generated in the portal
    - The `realm` is the **Redirect URI** you entered in the portal without the route. (example: http//localhost:3000)
    - The `issuer` is where you append the **Application Id** after, as an example: https://sts.windows.net/96702724-991f-4576-bc90-be9862749ac5/

- Next open `app.js` file in the root of the proejct and add the follwing call to invoke the `OIDStrategy` strategy that comes with `passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy; ```

- После этого используйте стратегию, которую мы только что использовали в ссылке, чтобы обрабатывать запросы на вход.

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 

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
    skipUserProfile: true // doesn't fetch user profile
  },
  function(iss, sub, email, claims, profile, accessToken, refreshToken, done) {
    log.info('We received claims of: ', claims);
    log.info('Example: Email address we received was: ', claims.preferred_username);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(claims.preferred_username, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(claims);
          return done(null, claims);
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
  done(null, user.preferred_username);
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
    if (user.preferred_username === email) {
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

- Наконец, добавим маршруты POST, которые будут передавать действительные запросы на вход в ядро `passport-azure-ad`:

```JavaScript

// Our POST routes (Section 3)

// POST /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.post('/auth/openid',
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

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});
```

## 4\. Использование Passport для выдачи запросов на вход и выход в Azure AD

Теперь приложение правильно настроено для взаимодействия с конечной точкой версии 2.0 с использованием протокола аутентификации OpenID Connect. `passport-azure-ad` выполнил всю трудоемкую обработку сообщений аутентификации, проверяя маркеры из Azure AD и поддерживая сеанс пользователя. Осталось дать пользователям возможность входа, выхода и сбора дополнительной информации о вошедшем в систему пользователе.

- Во-первых, добавим в файл `app.js` метод по умолчанию, а также метод входа, учетной записи и выхода.

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
    -	Маршрут `/` выполнит перенаправление в представление index.ejs, передавая пользователя в запросе (если он существует).
    - Маршрут `/account` сначала ***обеспечит аутентификацию*** (реализуемую ниже), а затем передаст пользователя в запросе, чтобы мы могли получить дополнительную информацию о пользователе.
    - Маршрут `/login` вызовет структуру проверки подлинности azuread-openidconnect из `passport-azuread` и, если проверка не будет пройдена, перенаправит пользователя назад к /login.
    - `/logout` просто вызовет logout.ejs (и маршрут), который очищает файлы cookie, и вернет пользователя к index.ejs


- В последней части `app.js` давайте добавим метод EnsureAuthenticated, который используется в `/account` выше.

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

- Наконец, создадим сам сервер в `app.js`.

```JavaScript

app.listen(3000);

```


## 5\. Создание представлений и маршрутов в Express для отображения пользователя на веб-сайте

Мы завершили `app.js`. Теперь надо просто добавить маршруты и представления, которые отобразят получаемую информацию для пользователя, а также обработают созданные нами маршруты `/logout` и `/login`.

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

- Создайте представление `/views/index.ejs` в корневом каталоге. Это простая страница, которая будет вызывать наши методы входа и выхода и позволит получать информацию об учетной записи. Обратите внимание, что можно использовать условный `if (!user)`, так как имя пользователя, передаваемое в запросе, свидетельствует о том, что у нас есть вошедший пользователь.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
	<h2>Hello, <%= user.name %>.</h2>
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
<p>displayName: <%= user.name %></p>
<p>Profile ID: <%= user.sub %></p>
<a href="/logout">Log Out</a>
<% } %>
```

- Наконец, давайте все красиво оформим, добавив макет. Создайте представление /views/layout.ejs в корневом каталоге.

```JavaScript

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

Запустите `node app.js` и перейдите к `http://localhost:3000`.


Выполните вход с личной учетной записью Майкрософт, рабочей или учебной учетной записью и обратите внимание на то, как удостоверение пользователя отображается в списке /account. Теперь у вас есть веб-приложение, защищенное с помощью стандартных отраслевых протоколов, которое может проверять подлинность пользователей с помощью личных, рабочих и учебных учетных записей.

##Дальнейшие действия

Для справки следует отметить, что готовый пример (без ваших значений конфигурации) в виде ZIP-файла находится [здесь](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Также можно клонировать его с GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Теперь можно перейти к более сложным темам. Можно попробовать:

[Защита веб-API с помощью модели приложения версии 2.0 в Node.js >>](active-directory-v2-devquickstarts-webapi-nodejs.md)

Дополнительные ресурсы: [Предварительная версия модели приложений 2.0 >>](active-directory-appmodel-v2-overview.md) [Тег StackOverflow azure-active-directory >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=August15_HO9-->
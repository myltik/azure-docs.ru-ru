<properties
	pageTitle="Приступая к работе с Azure AD на AngularJS | Microsoft Azure"
	description="Практическое руководство по созданию одностраничного приложения на AngularJS, которое интегрируется с Azure AD для входа в систему и вызывает методы программных интерфейсов приложения, защищенные Azure AD, по протоколу OAuth."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>


# Защита одностраничных приложений AngularJS с помощью Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD облегчает разработчикам реализацию входа и выхода пользователей и защищает вызовы методов интерфейса API в одностраничных приложениях, используя протокол OAuth. Также разработчик получает возможность осуществлять проверку подлинности пользователей с помощью их учетных записей в Active Directory и безопасно использовать любые веб-API, защищенные Azure AD, такие как интерфейсы Office 365 API или интерфейс Azure API.

Для JavaScript-приложений, выполняющихся в браузере, Azure AD предоставляет библиотеку проверки подлинности Active Directory — adal.js. Единственное предназначение adal.js — упростить для разработчика процесс получения маркеров доступа. Чтобы показать, насколько это просто, создадим приложение To Do List (список дел) на AngularJS, которое:

- Обеспечивает функцию входа пользователя в приложение, используя Azure AD как поставщика удостоверений.
- Отображает некоторые сведения о пользователе.
- Обеспечивает безопасный вызов методов программного интерфейса приложения To Do List , используя маркеры носителя из Azure AD.
- Обеспечивает функцию выхода пользователя из приложения.

Для создания полного рабочего приложения необходимо:

2. зарегистрировать свое приложение в Azure AD;
3. Установить библиотеку ADAL и настроить одностраничное приложение (SPA).
5. Использовать библиотеку ADAL для защиты страниц в SPA.

Чтобы начать работу, [загрузите каркас приложения](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) или [завершенный пример](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Вам также потребуется клиент Azure AD, в котором можно будет создавать пользователей и зарегистрировать приложение. Если у вас еще нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).

## *1. Регистрация приложения DirectorySearcher*
Чтобы приложение могло осуществлять проверку подлинности пользователей и получать маркеры, необходимо сначала зарегистрировать его в вашем клиенте Azure AD:

-	Войдите на [Портал управления Azure](https://manage.windowsazure.com).
-	В левой панели навигации щелкните **Active Directory**.
-	Выберите клиента, в котором будет зарегистрировано приложение.
-	Перейдите на вкладку **Приложения** и нажмите кнопку **Добавить** в нижней панели.
-	Следуйте инструкциям на экране, а затем создайте новое **Веб-приложение и/или веб-API**.
    -	**Имя** приложения является его описанием для конечных пользователей.
    -	**URI перенаправление** — место, куда Azure AD возвращает маркеры. Значение по умолчанию: `https://localhost:44326/`
-	После завершения регистрации служба Azure AD присваивает приложению уникальный **Идентификатор клиента**. Это значение понадобится в следующих разделах, поэтому скопируйте его с вкладки **Настройка**.
- Библиотека adal.js использует неявный поток OAuth для взаимодействия с Azure AD. Подключите этот неявный поток к своему приложению, выполнив следующие действия:
    - Загрузите манифест приложения, щелкнув **Управление манифестом**.
    - Откройте манифест и найдите свойство `oauth2AllowImplicitFlow`. Задайте для него значение `true`.
    - Сохраните и выгрузите манифест приложения, еще раз нажав **Управление манифестом**.

## *2. Установка библиотеки ADAL и настройка SPA*
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку adal.js и написать код для работы с удостоверением.

-	Добавьте adal.js в проект TodoSPA с помощью консоли диспетчера пакетов:
  - Загрузите файл [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) и добавьте его в каталог проекта `App/Scripts/`.
  - Загрузите файл [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) и добавьте его в каталог проекта `App/Scripts/`.
  - До закрывающего тега `</body>` в `index.html` вставьте строки загрузки сценариев:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-	Для того чтобы серверная часть одностраничного приложения To- Do List могла принимать маркеры из браузера, ей требуются сведения о параметрах регистрации приложения. В проекте TodoSPA откройте файл `web.config`. Замените значения элементов в разделе `<appSettings>` на значения, введенные на портале Azure. Ваш код будет ссылаться на эти значения при каждом использовании ADAL.
    -	`ida:Tenant` — это имя вашего клиента Azure AD, например "contoso.onmicrosoft.com".
    -	`ida:Audience` должно быть скопированным из портала значением **Идентификатор клиента** приложения.

## *3. Использование библиотеки ADAL для защиты страниц в SPA*
Библиотека adal.js создана для интеграции с провайдерами AngularJS маршрутизации и HTTP, что позволяет защищать отдельные представления в вашем SPA.

- В файл `App/Scripts/app.js` добавьте код для привязки модуля adal.js:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Также в `App/Scripts/app.js` добавьте код инициализации `adalProvider` конфигурационными значениями, заданными при регистрации приложения:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Теперь для обеспечения безопасности представления `TodoList` приложения требуется добавить только одну строку кода — `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

В итоге получается защищенное одностраничное приложение с возможностью регистрировать пользователей и выполнять защищенные запросы с маркером носителя к его серверному API. Когда пользователь щелкает ссылку `TodoList`, библиотека adal.js выполнит автоматическое перенаправление на Azure AD для выполнения входа, если это необходимо. К тому же adal.js автоматически добавляет значение access\_token ко всем запросам AJAX, которые передаются серверной части приложения. Все вышеупомянутое — необходимый минимум для создания SPA с помощью библиотеки adal.js, но существует ряд других функций, которые полезны в одностраничных приложениях:

- Для явной отправки запросов на вход и выход можно определить в контроллерах функции, которые будут вызывать библиотеку adal.js. В `App/Scripts/homeCtrl.js` добавьте:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- В интерфейсе приложения можно также предоставить сведения о пользователе. Служба ADAL уже добавлена к контроллеру `userDataCtrl`, поэтому доступ к объекту `userInfo` можно получить в связанном представлении. Файл `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Также существует множество сценариев, с помощью которых необходимо узнать, зарегистрирован пользователь или нет. Для сбора этой информации также можно использовать объект `userInfo`. Например, в `index.html` можно отображать кнопку "Login" (Вход) или кнопку "Logout" (Выход) на основе состояния проверки подлинности:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Поздравляем! Ваше одностраничное приложение, интегрированное с Azure AD, готово. Оно может проверять подлинность пользователей, безопасно вызывать серверные функции по протоколу OAuth 2.0 и получать основные сведения о пользователе. Если же вы этого еще не сделали, пришло время добавить в клиент нескольких пользователей. Запустите одностраничное приложение To Do List и выполните вход как один из пользователей. Добавьте задачи в список дел, выйдите и войдите снова.

Библиотека adal.js упрощает включение в приложение всех этих типичных функций работы с удостоверением. Методы этой библиотеки выполняют всю черновую работу — управление кэшем, поддержку протокола OAuth, предоставление пользователю диалогового окна входа, обновление маркеров с истекшим сроком действия и многое другое.

Завершенный образец (без ваших настроек) вы можете загрузить [отсюда](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Теперь можно приступить к изучению других сценариев. Можно попробовать:

[Вызвать веб-API CORS из SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0921_2016-->
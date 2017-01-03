---
title: "Приступая к работе с Azure AD на AngularJS | Документация Майкрософт"
description: "Практическое руководство по созданию одностраничного приложения на AngularJS, которое интегрируется с Azure AD для входа в систему и вызывает методы программных интерфейсов приложения, защищенные Azure AD, по протоколу OAuth."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 1865043ca9c9019b9813f11eb4a55f7f16d79287
ms.openlocfilehash: f2ee67fa55bf71eefb6c1b156b43fa3482f5dbe0


---
# <a name="securing-angularjs-single-page-apps-with-azure-ad"></a>Защита одностраничных приложений AngularJS с помощью Azure AD
[!INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD облегчает разработчикам реализацию входа и выхода пользователей и защищает вызовы методов интерфейса API в одностраничных приложениях, используя протокол OAuth.  Также разработчик получает возможность осуществлять проверку подлинности пользователей с помощью их учетных записей в Active Directory и безопасно использовать любые веб-API, защищенные Azure AD, такие как интерфейсы Office 365 API или интерфейс Azure API.

Для JavaScript-приложений, выполняющихся в браузере, Azure AD предоставляет библиотеку проверки подлинности Active Directory — adal.js.  Единственное предназначение adal.js — упростить для разработчика процесс получения маркеров доступа.  Чтобы показать, насколько это просто, создадим приложение To Do List (список дел) на AngularJS, которое:

* Обеспечивает функцию входа пользователя в приложение, используя Azure AD как поставщика удостоверений.
* Отображает некоторые сведения о пользователе.
* Обеспечивает безопасный вызов методов программного интерфейса приложения To Do List , используя маркеры носителя из Azure AD.
* Обеспечивает функцию выхода пользователя из приложения.

Для создания полного рабочего приложения необходимо:

1. зарегистрировать свое приложение в Azure AD;
2. Установить библиотеку ADAL и настроить одностраничное приложение (SPA).
3. Использовать библиотеку ADAL для защиты страниц в SPA.

Чтобы начать работу, [скачайте схему приложения](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) или [скачайте готовый пример](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Вам также потребуется клиент Azure AD, в котором можно создавать пользователей и регистрировать приложение.  Если у вас еще нет клиента, [узнайте, как его получить](active-directory-howto-tenant.md).

## <a name="1-register-the-directorysearcher-application"></a>*1. Регистрация приложения DirectorySearcher*
Чтобы приложение могло осуществлять проверку подлинности пользователей и получать маркеры, необходимо сначала зарегистрировать его в вашем клиенте Azure AD:

1. Войдите на [портал Azure](https://portal.azure.com).
2. Выберите клиент Azure AD, щелкнув имя своей учетной записи в правом верхнем углу страницы.
3. В области навигации слева щелкните **Azure Active Directory**.
4. Щелкните **Регистрация приложений** и нажмите кнопку **Добавить**.
5. Введите понятное имя для приложения, например TodoListService, выберите "Веб-приложение и/или веб-API" в качестве типа приложения.
6. В качестве URL-адреса единого входа введите базовый URL-адрес образца, например `https://localhost:8080`.
7. После завершения регистрации система Azure AD присвоит приложению уникальный идентификатор клиента (код приложения). Это значение вам понадобится в следующих разделах, поэтому не забудьте скопировать его на странице приложения.
8. На странице параметров настройте **URL-адрес ответа**, который будет использоваться в качестве расположения для возврата маркеров из Azure AD. Значение по умолчанию: `https://localhost:44326/`

* Библиотека adal.js использует неявный поток OAuth для взаимодействия с Azure AD.  Подключите этот неявный поток к своему приложению, выполнив следующие действия:
  * Щелкните **Манифест** на странице приложения, чтобы открыть редактор файлов манифеста, и найдите свойство `oauth2AllowImplicitFlow`. Задайте для него значение `true`.
  * Сохраните манифест.

## <a name="2-install-adal--configure-the-spa"></a>*2. Установка библиотеки ADAL и настройка SPA*
Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку adal.js и написать код для работы с удостоверением.

* Добавьте adal.js в проект TodoSPA с помощью консоли диспетчера пакетов:
  * Скачайте файл [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) и добавьте его в каталог проекта `App/Scripts/`.
  * Скачайте файл [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) и добавьте его в каталог проекта `App/Scripts/`.
  * До закрывающего тега `</body>` in `index.html`вставьте строки загрузки сценариев:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

* Для того чтобы серверная часть одностраничного приложения To- Do List могла принимать маркеры из браузера, ей требуются сведения о параметрах регистрации приложения. В проекте TodoSPA откройте файл `web.config`.  Замените значения элементов в разделе `<appSettings>` на значения, введенные на портале Azure.  Ваш код будет ссылаться на эти значения при каждом использовании ADAL.
  * `ida:Tenant` — это имя вашего клиента Azure AD, например "contoso.onmicrosoft.com".
  * `ida:Audience` должно быть скопированным из портала значением **Идентификатор клиента** приложения.

## <a name="3----use-adal-to-secure-pages-in-the-spa"></a>*3.    Использование библиотеки ADAL для защиты страниц в SPA*
Библиотека adal.js создана для интеграции с провайдерами AngularJS маршрутизации и HTTP, что позволяет защищать отдельные представления в вашем SPA.

* В файл `App/Scripts/app.js` добавьте код для привязки модуля adal.js:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
* Также в `App/Scripts/app.js` добавьте код инициализации `adalProvider` конфигурационными значениями, заданными при регистрации приложения:

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
* Теперь для обеспечения безопасности представления `TodoList` приложения требуется добавить только одну строку кода — `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

В итоге получается защищенное одностраничное приложение с возможностью регистрировать пользователей и выполнять защищенные запросы с маркером носителя к его серверному API.  Когда пользователь щелкает ссылку `TodoList`, библиотека adal.js выполнит автоматическое перенаправление на Azure AD для выполнения входа, если это необходимо.  К тому же adal.js автоматически добавляет значение access_token ко всем запросам AJAX, которые передаются серверной части приложения.  Все вышеупомянутое —  необходимый минимум для создания SPA с помощью библиотеки adal.js, но существует ряд других функций, которые полезны в одностраничных приложениях:

* Для явной отправки запросов на вход и выход можно определить в контроллерах функции, которые будут вызывать библиотеку adal.js.  В `App/Scripts/homeCtrl.js` добавьте:

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
* В интерфейсе приложения можно также предоставить сведения о пользователе.  Служба ADAL уже добавлена к контроллеру `userDataCtrl`, поэтому доступ к объекту `userInfo` можно получить  в связанном представлении. Файл `App/Views/UserData.html`:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

* Также существует множество сценариев, с помощью которых необходимо узнать, зарегистрирован пользователь или нет.  Для сбора этой информации также можно использовать объект `userInfo` .  Например, в `index.html` можно отображать кнопку "Login" (Вход) или кнопку "Logout" (Выход) на основе состояния проверки подлинности:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Поздравляем! Ваше одностраничное приложение, интегрированное с Azure AD, готово.  Оно может проверять подлинность пользователей, безопасно вызывать серверные функции по протоколу OAuth 2.0 и получать основные сведения о пользователе.  Если же вы этого еще не сделали, пришло время добавить в клиент нескольких пользователей.  Запустите одностраничное приложение To Do List и выполните вход как один из пользователей.  Добавьте задачи в список дел, выйдите и войдите снова.

Библиотека adal.js упрощает включение в приложение всех этих типичных функций работы с удостоверением.  Методы этой библиотеки выполняют всю черновую работу — управление кэшем, поддержку протокола OAuth, предоставление пользователю диалогового окна входа, обновление маркеров с истекшим сроком действия и многое другое.

Завершенный образец (без ваших настроек) вы можете загрузить [отсюда](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).  Теперь можно приступить к изучению других сценариев.  Можно попробовать:

[Вызов веб-API CORS из SPA >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]



<!--HONumber=Dec16_HO4-->



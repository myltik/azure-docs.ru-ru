<properties
	pageTitle="Приступая к работе с Azure AD версии 2.0 на AngularJS | Microsoft Azure"
	description="Как создать одностраничное приложение AngularJS, которое поддерживает вход пользователей в систему с помощью личной учетной записи Майкрософт, а также рабочей или учебной учетной записи."
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


# Добавление функции входа в одностраничное приложение AngularJS — версия для .NET

В этой статье мы добавим в приложение AngularJS функцию входа с учетными записями на платформе Майкрософт с помощью конечной точки Azure Active Directory версии 2.0. Конечная точка версии 2.0 обеспечивает единую интеграцию в приложении и аутентификацию пользователей как с личными, так и с рабочими или учебными учетными записями.

Наш пример — это простое одностраничное приложение со списком дел, которое сохраняет задачи в серверной части REST API. Оно написано на платформе MVC .NET 4.5 и защищено с помощью токенов носителя OAuth из Azure AD. Приложение AngularJS будет использовать нашу библиотеку проверки подлинности JavaScript с открытым исходным кодом [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) для обработки всего процесса входа в систему и получения токенов для вызова интерфейса API REST. Эта модель может применяться для аутентификации в других интерфейсах REST API, например [Microsoft Graph](https://graph.microsoft.com).

> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).

## Загрузить

Чтобы начать работу, необходимо загрузить и установить Visual Studio. Затем можно клонировать или [загрузить](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) скелет приложения:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet.git
```

Скелет приложения включает в себя весь код шаблона простого приложения AngularJS, но в нем отсутствуют все компоненты, связанные с удостоверениями. Если не хотите следовать учебнику, вместо этого можно клонировать или [загрузить](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-DotNet/archive/complete.zip) готовый пример.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet.git
```

## регистрация приложения;

Во-первых, создайте приложение на [портале регистрации приложений](https://apps.dev.microsoft.com) или выполните следующие [подробные шаги](active-directory-v2-app-registration.md). Не забудьте:

- Добавьте **веб-платформу** для своего приложения.
- Введите правильный **универсальный код ресурса (URI) перенаправления**. Значение по умолчанию для этого примера: `https://localhost:44326/`.
- Оставьте включенным флажок **Разрешить неявный поток**.

Запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.

## Установка adal.js
Для запуска перейдите в загруженный проект и установите библиотеку adal.js. Если у вас установлен [bower](http://bower.io/), можно просто выполнить эту команду. В случае несоответствия версий каких-либо зависимостей просто выберите наиболее актуальную версию.
```
bower install adal-angular#experimental
```

Кроме того, вы можете вручную загрузить [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) и [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js). Добавьте оба файла в каталог `app/lib/adal-angular-experimental/dist` проекта `TodoSPA`.

Теперь откройте проект в Visual Studio и в конце главной страницы добавьте код adal.js:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## Настройка REST API

Пока мы все настраиваем, давайте обеспечим работу серверной части REST API. В корневой папке проекта откройте `web.config` и замените значение `audience`. Интерфейс REST API будет использовать это значение для проверки токенов, получаемых от приложения Angular по запросам AJAX.

```xml
<!--web.config-->

...

    <appSettings>
        <add key="ida:Audience" value="[Your-application-id]" />
    </appSettings>
    
...
```

На этом мы остановимся и не будем углубляться в обсуждение принципов работы REST API. Не стесняйтесь изучать код, но если вы хотите узнать больше о защите веб-API с помощью Azure AD, ознакомьтесь с [этой статьей](active-directory-v2-devquickstarts-dotnet-api.md).

## Вход пользователей
Настало время написать код для проверки подлинности. Вы уже могли заметить, что в adal.js имеется поставщик AngularJS, который хорошо работает с механизмами маршрутизации Angular. Начните с добавления в приложение модуля adal:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Теперь вы можете инициализировать `adalProvider` с помощью ИД приложения:

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

Отлично, теперь у adal.js есть все сведения, необходимые для защиты приложения и входа пользователей. Принудительный вход с определенным маршрутом в приложении определяется всего одной строкой кода:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Когда пользователь щелкает ссылку `TodoList`, библиотека adal.js при необходимости автоматически выполнит перенаправление на Azure AD для выполнения входа. Вы можете явно отправлять запросы на выполнение входа и выхода, вызывая adal.js в контроллерах:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## Отображение сведений о пользователе
Теперь, когда пользователь выполнил вход, вам может потребоваться доступ к данным проверки подлинности в приложении. Adal.js предоставляет эти сведения в объекте `userInfo`. Чтобы получить доступ к этому объекту в представлении, сначала добавьте adal.js в корневую область соответствующего контроллера:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Затем можно обращаться напрямую к объекту `userInfo` в представлении:

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Можно также использовать объект `userInfo`, чтобы определить, выполнил пользователь вход или нет.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## Вызов REST API
Наконец пришло время получить токены и вызвать REST API для создания, чтения, обновления и удаления задач. И знаете что? Вам не придется делать *ничего*. Adal.js автоматически обеспечит получение, кэширование и обновление токенов. Библиотека также позаботится о присоединении этих токенов к исходящим запросам AJAX, отправляемым в REST API.

Как именно это работает? Это все благодаря магии [перехватчиков AngularJS](https://docs.angularjs.org/api/ng/service/$http), которые позволяют adal.js преобразовывать входящие и исходящие http-сообщения. Кроме того, adal.js предполагает, что все запросы отправляются в один домен, так как окно должно использовать токены, предназначенные для того же идентификатора приложения, что и у приложения AngularJS. Именно поэтому мы использовали один и тот же идентификатор приложения в приложении Angular и в REST API NodeJS. Конечно, можно переопределить это поведение и сделать так, чтобы библиотека adal.js при необходимости получала токены для других интерфейсов REST API. Но в нашем упрощенном примере подойдут значения по умолчанию.

Ниже приведен фрагмент, который показывает, насколько просто отправлять запросы с токенами носителя из Azure AD.

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Поздравляем! Ваше одностраничное приложение, интегрированное с Azure AD, готово. Протестируйте его. Приложение может проверять подлинность пользователей, безопасно обращаться к REST API серверной части с помощью OpenID Connect и получать основные сведения о пользователе. По умолчанию оно поддерживает всех пользователей с учетной записью Майкрософт или рабочей либо учебной учетной записью в Azure AD. Запустите приложение и в адресной строке браузера введите `https://localhost:44326/`. Войдите с личной учетной записью Майкрософт либо рабочей или учебной учетной записью. Добавьте задачи в список дел и выйдите. Попробуйте войти с учетной записью другого типа. Если вам нужен клиент Azure AD для создания рабочих или школьных учетных записей, [узнайте здесь, как его получить](active-directory-howto-tenant.md) (предоставляется бесплатно).

Чтобы продолжить изучение конечной точки версии 2.0, вернитесь к нашему [руководству разработчика для версии 2.0](active-directory-appmodel-v2-overview.md). Дополнительные ресурсы:

- [Образцы Azure на GitHub >>](https://github.com/Azure-Samples)
- [Azure AD при переполнении стека >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Документация по Azure AD [на сайте Azure.com](https://azure.microsoft.com/documentation/services/active-directory/)

## Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

<!---HONumber=AcomDC_0921_2016-->
<properties
	pageTitle="Приступая к работе с Azure AD на AngularJS | Microsoft Azure"
	description="Как создать одностраничное приложение Angular JS, которое поддерживает вход пользователей в систему с помощью личной и рабочей или школьной учетной записи Майкрософт."
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
	ms.date="11/19/2015"
	ms.author="dastrock"/>


# Предварительная версия модели приложений 2.0: реализация входа в одностраничном приложении AngularJS — NodeJS

В эту статью мы добавим вход с учетными записями Майкрософт в приложение AngularJS с помощью модели приложения Azure Active Directory версии 2.0. Модель приложения версии 2.0 позволяет выполнять единую интеграцию в приложении и проверять подлинность пользователей как с личными, так и с рабочими или школьными учетными записями.

Этот пример представляет собой простое одностраничное приложение со списком дел, которое сохраняет задачи в серверной части API REST, написанное на NodeJS и защищенное с помощью токенов носителя OAuth из Azure AD. Приложение AngularJS будет использовать нашу библиотеку проверки подлинности JavaScript с открытым исходным кодом [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) для обработки всего процесса входа в систему и получения токенов для вызова интерфейса API REST. Тот же шаблон может применяться для проверки подлинности в других интерфейсах API REST, например [Microsoft Graph](https://graph.microsoft.com) или API диспетчера ресурсов Azure.

## Загрузить

Чтобы начать работу, необходимо загрузить и установить [node.js](https://nodejs.org). Затем можно клонировать или [загрузить](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) скелет приложения:

```
git clone --branch skeleton https://github.com/AzureADQuickStarst/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

Скелет приложения включает в себя весь код шаблона простого приложения AngularJS, но в нем отсутствуют все компоненты, связанные с удостоверениями. Если не хотите следовать учебнику, вместо этого можно клонировать или [загрузить](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) готовый пример.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## регистрация приложения;

Во-первых, создайте приложение на [портале регистрации приложений](https://apps.dev.microsoft.com) или выполните следующие [подробные шаги](active-directory-v2-app-registration.md). Не забудьте:

- Добавьте **веб-платформу** для своего приложения.
- Введите правильный **универсальный код ресурса (URI) перенаправления**. Значение по умолчанию для этого примера: `http://localhost:8080`.
- Оставьте включенным флажок **Разрешить неявный поток**. 

Запишите назначенный вашему приложению **идентификатор**. Он вскоре вам понадобится.

## Установка adal.js
Для запуска перейдите в загруженный проект и установите библиотеку adal.js. Если у вас установлен [bower](http://bower.io/), можно просто выполнить эту команду. Для любого несоответствия версий зависимостей просто выберите обновленную версию. ```
bower install adal-angular#experimental
```

Кроме того, можно вручную загрузить [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) и [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js). Добавьте оба файла в каталог `app/lib/adal-angular-experimental/dist`.

Теперь откройте проект в своем любимом текстовом редакторе и загрузите adal.js в конце тела страницы:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## Настройка API REST

Пока мы все настраиваем, обеспечим работу серверной части API REST. В командной строке установите все необходимые пакеты, выполнив команду (убедитесь, что находитесь в каталоге верхнего уровня проекта):

```
npm install
```

Теперь откройте `config.js` и замените значение `audience`:

```js
exports.creds = {
     
     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',
	 
	 ...
}
```

Интерфейс API REST будет использовать это значение для проверки токенов, получаемых от приложения Angular по запросам AJAX. Обратите внимание, что этот простой API REST хранит данные в памяти — поэтому каждый раз при остановке сервера все ранее созданные задачи будут потеряны.

Вот и все время, которое мы потратим на обсуждение принципов работы API REST. Не стесняйтесь изучать код, но если вы хотите узнать больше о защите веб-API с помощью Azure AD, ознакомьтесь с [этой статьей](active-directory-v2-devquickstarts-node-api.md).

## Вход пользователей
Время для написания кода удостоверения. Вы уже заметили, что adal.js содержит поставщика AngularJS, который хорошо работает с механизмами маршрутизации Angular. Начните с добавления модуля adal в приложение:

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

Отлично, теперь adal.js содержит все сведения, необходимые для защиты приложения и входа пользователей. Для принудительного входа для конкретного маршрута в приложении нужна всего лишь одна строка кода:

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

Когда пользователь щелкает ссылку `TodoList`, библиотека adal.js при необходимости автоматически выполнит перенаправление на Azure AD для выполнения входа. Можно также явно отправлять запросы входа и выхода путем вызова adal.js в контроллерах:

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
Теперь, когда пользователь выполнил вход, вам может потребоваться доступ к данным проверки подлинности выполнившего вход пользователя в приложении. Adal.js предоставляет эти сведения в объекте `userInfo`. Для доступа к этому объекту в представлении сначала добавьте adal.js в корневую область соответствующего контроллера:

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

## Вызов API REST
Наконец пришло время для получения некоторых токенов и вызова API REST для создания, чтения, обновления и удаления задачи. И знаете что? Вам не придется делать *ничего*. Adal.js автоматически обеспечит получение, кэширование и обновление токенов. Он также позаботится о присоединении этих токенов к исходящим запросам AJAX, отправляемым в API REST.

Как именно это работает? Это все благодаря магии [перехватчиков AngularJS](https://docs.angularjs.org/api/ng/service/$http), которые позволяют adal.js преобразовывать входящие и исходящие http-сообщения. Кроме того, adal.js предполагает, что любой запрос, отправленный в тот же домен, что и окно, должен использовать токены, предназначенные для того же идентификатора приложения, что и у приложения AngularJS. Именно поэтому мы использовали один и тот же идентификатор приложения в приложении Angular и в API REST NodeJS. Конечно, можно переопределить это поведение и скомандовать adal.js получать токены для других интерфейсов API REST при необходимости, но для этого простого сценария подойдут значения по умолчанию.

Ниже приведен фрагмент, который показывает, насколько просто отправлять запросы с токенами носителя из Azure AD.

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Поздравляем! Ваше одностраничное приложение, интегрированное с Azure AD, готово. Давайте, попробуйте. Приложение может проверять подлинность пользователей, безопасно вызывать серверную часть API REST с помощью OpenID Connect и получать основные сведения о пользователе. По умолчанию оно поддерживает любого пользователя с личной учетной записью Майкрософт или рабочей либо школьной учетной записью из Azure AD. Попробуйте приложение, выполнив:

```
node server.js
```

Откройте браузер и перейдите по адресу `http://localhost:8080`. Войдите с личной учетной записью Майкрософт либо рабочей или школьной учетной записью. Добавьте задачи в список дел и выйдите. Попробуйте войти с учетной записью другого типа. Если вам нужен клиент Azure AD для создания рабочих или школьных учетных записей, [узнайте здесь, как его получить](active-directory-howto-tenant.md) (предоставляется бесплатно).

Чтобы продолжить изучение предварительной версии модели приложения версии 2.0, вернитесь к нашему [руководству разработчика по версии 2.0](active-directory-appmodel-v2-overview.md). Дополнительные ресурсы:

- [Образцы Azure на GitHub >>](https://github.com/Azure-Samples)
- [Azure AD при переполнении стека >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Документация по Azure AD [на сайте Azure.com](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=AcomDC_1125_2015-->
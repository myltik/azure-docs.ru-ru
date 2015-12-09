<properties
	pageTitle="Проверка подлинности пользователей для приложений API в службе приложений Azure | Microsoft Azure"
	description="Узнайте, как защитить приложение API в службе приложений Azure путем предоставления доступа только тем пользователям, которые прошли проверку подлинности."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/30/2015"
	ms.author="tdykstra"/>

# Проверка подлинности пользователя для приложений API в службе приложений Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Обзор

В этом руководстве показано, как использовать функции проверки подлинности и авторизации службы приложений Azure для защиты приложений API, а также, как использовать приложение API от имени пользователей. В этом руководстве используется поставщик проверки подлинности Azure Active Directory и пример клиента, который представляет собой одностраничное приложение AngularJS, работающее в браузере.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
Это третье руководство в серии учебных материалов для работы с приложениями API в службе приложений Azure. Дополнительные сведения об этой серии см. в статье [Приступая к работе с приложениями API и ASP.NET в службе приложений Azure](app-service-api-dotnet-get-started.md).

## Проверка подлинности и авторизация в службе приложений Azure

Служба приложений Azure предоставляет встроенные службы для проверки подлинности и авторизации. У вас есть возможность проводить проверку подлинности при помощи собственного кода, но служба приложений предоставляет готовое решение, которое позволяет свести к минимуму объем кода, который требуется написать и обслуживать.

Можно использовать любой из пяти поставщиков проверки подлинности, которые поддерживает служба приложений: Azure Active Directory, Facebook, Google, Twitter и учетная запись Microsoft. Можно также защитить API, написанный на любом языке, который поддерживает служба приложений: нет необходимости писать код в API, поскольку вы можете запросить имя пользователя или токен для получения доступа к нему.

Служба приложений Azure выполняет проверку подлинности, а авторизация выполняется на основе кода. Можно настроить службу приложений так, чтобы ваш API могли вызывать все пользователи или только те, которые прошли проверку подлинности. В любом случае служба приложений передает приложению информацию о проверке подлинности в заголовках HTTP. Эта информация может быть использована в коде для принятия решений об авторизации.

* В .NET API можно использовать атрибут `Authorize`, а для точной авторизации можно с легкостью написать код на основе утверждений. Информация об утверждениях заполняется автоматически в классах .NET.

* Для API, написанных на других языках, служба приложений передает токен JWT в заголовке авторизации HTTP-запроса. Кроме того, Azure задает некоторые специальные заголовки (например, `x-ms-client-principal-id`) для предоставления простого доступа к наиболее важным утверждениям.

Дополнительные сведения о службах проверки подлинности и авторизации в службе приложений Azure см. в статьях [Развертывание проверки подлинности и авторизации службы приложений](/blog/announcing-app-service-authentication-authorization/) и [Изменения приложений API службы приложений](app-service-api-whats-changed.md).

## Пример проекта ContactsList.Angular.AAD

При работе с этим руководством вы воспользуетесь примерами проектов и ресурсами Azure (приложением API и веб-приложением), которые были скачаны и созданы при работе с [первым руководством из этой серии](app-service-api-dotnet-get-started.md).

Проект ContactsList.Angular.AAD представляет собой клиент AngularJS, который содержит код для работы с Azure Active Directory. Код основан на примере AAD, который находится в репозитории [Azure-Samples/active-directory-angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp).

Код в проекте ContactsList.Angular.AAD структурирован иначе, чем в более простом проекте ContactsLists.Angular. Код, который вызывает API, находится в файле *app/scripts/contactsSvc.js* в проекте ContactsList.Angular.AAD.

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

Здесь метод `Get` помечен как `getItems`, а в контроллере (*app/scripts/contactsCtrl.js*) `getItems` подключен к `$scope.populate`.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

В представлении (*app/views/Contacts.html*) $scope.populate вызывается при инициализации.

		<div ng-init="populate()">

## Настройка подлинности и авторизации в Azure

1. На [портале Azure](https://portal.azure.com/) перейдите к колонке **приложения API**, созданного в первом руководстве, и щелкните **Параметры**.

2. Найдите раздел **Функции** и щелкните **Проверка подлинности и авторизация**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. В колонке **Проверка подлинности и авторизация** щелкните **Вкл.**.

4. В раскрывающемся списке **Действие, выполняемое, если запрос не прошел проверку подлинности** выберите **Войти с помощью Azure Active Directory**.

5. В разделе **Поставщики проверки подлинности** щелкните **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. В колонке **Параметры Azure Active Directory** щелкните **Автоматическое**.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Azure автоматически создаст приложение AAD в клиенте AAD. Запишите имя нового приложения AAD, поскольку позже вам необходимо будет выбрать его после перехода на классический портал Azure для получения идентификатора клиента нового приложения AAD.

7. Нажмите кнопку **ОК**.

10. В колонке **Проверка подлинности и авторизация** нажмите кнопку **Сохранить**.

8. Чтобы убедиться, что приложение API защищено, перейдите по URL-адресу приложения API + `/swagger`, как в первом руководстве, для использования пользовательского интерфейса Swagger.

	На этот раз вы будете перенаправлены на страницу входа.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. На [классическом портале Azure](https://manage.windowsazure.com/) перейдите в раздел **Azure Active Directory**.

12. На вкладке «Каталог» щелкните свой клиент AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Щелкните **Приложения > Приложение, принадлежащее моей компании**, а затем установите флажок.

	Кроме того, может потребоваться обновить страницу, чтобы увидеть новое приложение.

15. В списке приложений щелкните имя приложения, созданного в Azure после того, как вы активировали проверку подлинности для приложения API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Нажмите **Настроить**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. В нижней части страницы щелкните **Управление манифестом > Загрузить манифест** и сохраните файл в расположении, в котором его можно изменить.

16. В скачанном файле манифеста найдите свойство `oauth2AllowImplicitFlow`. Измените значение этого свойства с `false` на `true`, а затем сохраните файл.

16. Щелкните **Управление манифестом > Отправить манифест** и отправьте файл, который был обновлен на предыдущем шаге.

17. Оставьте эту страницу открытой, чтобы можно было копировать из и вставлять данные в нее и обновлять значения на странице при выполнении дальнейших шагов этого руководства.

## Настройка проектов Visual Studio для протокола HTTPS

1. В **обозревателе решений** щелкните проект ContactList.API, а затем в окне **Свойства** измените значение **SSL включен** на **True**.

2. Скопируйте URL-адрес SSL.

	![](./media/app-service-api-dotnet-user-principal-auth/enablessl.png)

3. Щелкните правой кнопкой мыши проект ContactsList.API и выберите пункт **Свойства**.

5. Щелкните вкладку **Веб**, вставьте URL-адрес SSL в поле **URL-адрес проекта** и сохраните внесенные изменения.

	![](./media/app-service-api-dotnet-user-principal-auth/setprojecturl.png)

1. Выполните те же шаги, чтобы включить протокол SSL для проекта ContactsList.Angular.AAD.

2. Установите проекты ContactsList.Angular.AAD и ContactsList.API в качестве запускаемых проектов и задайте параметры, согласно которым сначала будет запускаться ContactsList.API.

## Обновление URL-адреса конечной точки и параметров AAD в проекте ContactsList.Angular.AAD

7. В проекте ContactsList.Angular.AAD откройте файл *app/scripts/app.js*.

8. Убедитесь в том, что в коде, задающем переменную `endpoints`, указан правильный URL-адрес SSL для проекта ContactsList.API, и замените оба значения yourclientid на фактические значения идентификатора клиента приложения AAD на вкладке **Настройка** приложения AAD на классическом портале. URL-адрес конечной точки должен заканчиваться косой чертой.

	Код будет выглядеть примерно следующим образом:

		var endpoints = {
		    //"https://{your api app name}.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    "https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Кроме того, в *app.js* в коде `adalProvider.init` замените {URL-адрес клиента} и {идентификатор клиента} фактическими значениями.

	Код будет выглядеть примерно следующим образом:

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

10. В *app/scripts/contactsSvc.js* убедитесь, что для apiEndpoint задан правильный URL-адрес SSL для проекта ContactsList.API.

		//var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		var apiEndpoint = "https://localhost:44300";

## Обновление параметров AAD в проекте ContactsList.API

1. В проекте ContactsList.API откройте файл приложения *Web.config*.

2. В элементе appSettings задайте для ida:Authority значение https://login.windows.net/{URL-адрес клиента}, а для ida:ClientId — идентификатор клиента приложения AAD, как показано в следующем примере.

		<appSettings>
		  <add key="ida:Authority" value="https://login.windows.net/contoso.onmicrosoft.com" />
		  <add key="ida:ClientId" value="1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3" />
		</appSettings>

## Настройка приложения AAD для localhost 

1. На классическом портале на вкладке **Настройка** приложения AAD в поле **URL-адрес входа** вставьте URL-адрес SSL проекта ContactsList.Angular.AAD и удалите косую черту в конце.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurl.png)

3. В нижней части вкладки **Настройка** в поле **URL-адрес ответа** вставьте URL-адрес SSL проекта ContactsList.Angular.AAD, заменив уже заданное значение и оставив косую черту в конце.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurl.png)

4. Щелкните **Сохранить**.

## Локальный запуск проектов API и клиентов

При локальном запуске протокола HTTPS вы получите предупреждающее сообщение об SSL-сертификате IIS Express для localhost. Для локального запуска можно щелкнуть область за пределами этих сообщений. При желании можно пропустить этот раздел и перейти непосредственно к следующему разделу, в котором описана подготовка к запуску приложения и API в Azure.

Если у вас возникли проблемы при входе, попробуйте использовать другой браузер или ввести URL-адрес проекта AngularJS в окне в закрытом или анонимном режиме, например, `https://localhost:44301`.

5. В Visual Studio нажмите клавишу F5 для локального запуска проектов API и AngularJS.

	Откроется вкладка «Главная» приложения AngularJS.

10. Щелкните вкладку **Вход**.

	Вам будет предложено войти.

7. Войдите с помощью учетных данных пользователя, используемых в клиенте AAD.

10. Щелкните вкладку **Контакты**.

	Появится страница **Контакты**.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspagelocal.png)

11. Закройте окна браузера.

В коде проекта ContactsList.Angular.AAD для вызова API и отображения контактов требуется вход пользователя в систему. Тем не менее, механизм, который предотвращал бы осуществление вызовов API пользователями, не прошедшими проверку подлинности, отсутствует. Это можно проверить, запустив пользовательский интерфейс Swagger в окне браузера с URL-адресом SSL проекта ContactsList.API. API защищен от вызовов пользователей, не прошедших проверку подлинности, только при запуске в службе приложений Azure.

В следующих разделах вы настроите проекты и AAD для запуска клиента и API в службе приложений Azure, а также развернете и протестируете проекты в Azure.

## Настройка проекта ContactsList.Angular.AAD для вызова приложения API Azure

1. В проекте ContactsList.Angular.AAD откройте файл *app/scripts/app.js*.

2. Закомментируйте конечную точку localhost, раскомментируйте конечную точку Azure и замените {имя приложения API} именем вашего приложения API.

	Код будет выглядеть примерно следующим образом:

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

1. В файле *app/scripts/contactsSvc.js* измените URL-адрес той же конечной точки.

	Код будет выглядеть примерно следующим образом:

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";


## Настройка приложения AAD для веб-приложения Azure

1. На классическом портале на вкладке **Настройка** приложения AAD в поле **URL-адрес входа** удалите URL-адрес SSL проекта ContactsList.Angular.AAD и замените его на базовый URL-адрес веб-приложения без косой черты в конце. (Обратите внимание, что это URL-адрес веб-приложения, а не URL-адрес приложения API.)

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. В поле **URL-адрес ответа** замените URL-адрес SSL проекта ContactsList.Angular.AAD на базовый URL-адрес веб-приложения, оставив косую черту в конце.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Щелкните **Сохранить**.

## Развертывание проекта ContactsList.API в Azure

8. В **обозревателе решений** щелкните правой кнопкой мыши проект ContactsList.API и нажмите кнопку **Опубликовать**.

	В мастере **веб-публикации** откроется последний использованный профиль публикации этого проекта, который необходим для развертывания в созданном ранее приложении API.

7. Щелкните **Опубликовать**.

8. Закройте окно браузера, которое открывается автоматически.

## Развертывание проекта ContactsList.Angular.AAD в Azure

8. В **обозревателе решений** щелкните правой кнопкой мыши проект ContactsList.Angular.API и нажмите кнопку **Опубликовать**.

9. Щелкните **Служба приложений Microsoft Azure**.

10. В диалоговом окне **Служба приложений** в раскрывающемся списке **Подписка** выберите вашу подписку.

11. Разверните группу ресурсов, которая была создана для этого руководства, и выберите веб-приложение, созданное при работе со вторым руководством.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Нажмите кнопку **ОК**.

12. В мастере **веб-публикации** щелкните вкладку **Подключение**, а затем в поле **Конечный URL-адрес**, замените `http://` на `https://`.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

12. В мастере **веб-публикации** щелкните вкладку **Параметры**, разверните раздел **Параметры публикации файлов** и установите флажок **Удалить дополнительные файлы в месте назначения**.

7. Щелкните **Опубликовать**.

	Visual Studio развернет проект и откроет домашнюю страницу приложения в браузере.

## Тестирование веб-приложения AngularJS в Azure

8. Щелкните вкладку **Контакты**.

	Вам будет предложено войти.

9. Войдите с помощью учетных данных, используемых в клиенте AAD.

10. Появится страница **Контакты**.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

11. Чтобы убедиться, что приложение API защищено, перейдите по URL-адресу пользовательского интерфейса Swagger в окне браузера в закрытом или анонимном режиме.

	Вы будете перенаправлены на страницу входа.

	Внешний интерфейс теперь может вызвать API от имени пользователя, прошедшего проверку подлинности. Пользователи, не прошедшие проверку подлинности, не смогут вызвать API.

## Дальнейшие действия

В этом руководстве описывается ограничение доступа к приложению API с помощью проверки подлинности и авторизации службы приложений, которые позволяют вызывать его только пользователям, прошедшим проверку подлинности. В следующем руководстве этой серии вы узнаете, как [ограничить доступ к приложению API для сценариев взаимодействия служб](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->
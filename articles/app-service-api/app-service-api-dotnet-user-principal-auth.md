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
	ms.date="01/08/2016"
	ms.author="tdykstra"/>

# Проверка подлинности пользователя для приложений API в службе приложений Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Обзор

В этом руководстве показано, как использовать функции проверки подлинности и авторизации службы приложений Azure для защиты приложений API, а также как использовать приложение API от имени пользователей. В этом руководстве используется поставщик проверки подлинности Azure Active Directory, веб-API ASP.NET и клиент, который представляет собой одностраничное приложение AngularJS, работающее в браузере.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
## Проверка подлинности и авторизация в службе приложений

Введение в функции аутентификации, которые используются в этом руководстве, см. в предыдущем руководстве этой серии — [Проверка подлинности и авторизация для приложений API в службе приложений Azure](app-service-api-authentication.md).

## Как работать с этим руководством

Это руководство основано на примере приложения, которое вы загрузили и на основе которого создали приложение API в ходе работы с руководством [Приступая к работе с приложениями API и ASP.NET в службе приложений Azure](app-service-api-dotnet-get-started.md).

## Пример проекта ContactsList.Angular.AAD

Проект ContactsList.Angular.AAD из [примера приложения ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) представляет собой клиент AngularJS, который содержит код для работы с Azure Active Directory. Код проекта основан на примере AAD, который находится в репозитории [Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi).

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

Здесь метод `Get` обозначен как `getItems`. В контроллере (*app/scripts/contactsCtrl.js*) `getItems` указывает на `$scope.populate`.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

В представлении (*app/views/Contacts.html*) функция $scope.populate вызывается при инициализации.

		<div ng-init="populate()">

Дополнительный код для ведения журнала и использования маркера авторизации с запросами API можно найти в [библиотеке аутентификации в Azure Active Directory для JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js), в файлах *adal.js* и *adal-angular.js*.

Код в файле *app.js* передает функции `adalProvider.init` сведения о конфигурации и поставщика `$http`. Сведения о конфигурации содержат идентификатор клиентского приложения AAD, который относится к каждой конечной точке API, и идентификатор клиента, который относится к этому приложению AngularJS. Функция `init` добавляет перехватчики для поставщика `$http`, которые добавляют к запросам маркеры проверки подлинности.

		var endpoints = { 
		    //"https://{your api app name}.azurewebsites.net/": "{your client id}"
		    "https://localhost:44300/": "{your client id}"
		};

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: '{your tenant url}',
		        clientId: '{your client id}',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		        //cacheLocation: 'localStorage', // enable this for 
		    },
		    $httpProvider
		    );

## Настройка подлинности и авторизации в Azure

1. На [портале Azure](https://portal.azure.com/) перейдите в колонку **Приложение API** того приложения, которое требуется защитить, чтобы только пользователи, прошедшие проверку подлинности, могли его вызывать. (Для этого руководства выберите то приложение API, в которое ранее был развернут проект ContactsList.API.)

2. Щелкните **Настройки**.

2. Найдите раздел **Функции** и щелкните **Проверка подлинности и авторизация**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. В колонке **Проверка подлинности и авторизация** щелкните **Вкл.**

4. В раскрывающемся списке **Действие, выполняемое, если запрос не прошел проверку подлинности** выберите **Войти с помощью Azure Active Directory**.

5. В разделе **Поставщики проверки подлинности** щелкните **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. В колонке **Параметры Azure Active Directory** щелкните **Автоматическое**.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Этот параметр означает, что Azure автоматически создаст приложение AAD в клиенте AAD. Запишите имя нового приложения AAD, поскольку позже вам необходимо будет выбрать его после перехода на классический портал Azure для получения идентификатора клиента нового приложения AAD.

7. Нажмите кнопку **ОК**.

10. В колонке **Проверка подлинности и авторизация** нажмите кнопку **Сохранить**.

8. Чтобы убедиться, что приложение API защищено, перейдите по URL-адресу приложения API с добавлением `/swagger`. Как вы помните, в первом руководстве мы так обращались к пользовательскому интерфейсу Swagger.

	На этот раз вы будете перенаправлены на страницу входа.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. На [классическом портале Azure](https://manage.windowsazure.com/) перейдите в раздел **Azure Active Directory**.

	Нужно использовать именно классический портал, так как в текущей версии портала Azure еще не доступны некоторые параметры Azure Active Directory, которые нам нужны.

12. На вкладке **Каталог** щелкните свой клиент AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Щелкните **Приложения > Приложения, принадлежащие моей компании**, а затем установите флажок.

	Кроме того, может потребоваться обновить страницу, чтобы увидеть новое приложение.

15. В списке приложений щелкните имя приложения, созданного в Azure после того, как вы активировали проверку подлинности для приложения API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Нажмите **Настроить**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. В нижней части страницы щелкните **Управление манифестом > Загрузить манифест** и сохраните файл в расположении, в котором его можно изменить.

16. В скачанном файле манифеста найдите свойство `oauth2AllowImplicitFlow`. Измените значение этого свойства с `false` на `true`, а затем сохраните файл.

	Это нужно для того, чтобы был возможен доступ из одностраничного приложения JavaScript. При таком значении параметра токен носителя Oauth 2.0 возвращается в виде фрагмента URL-адреса.

16. Щелкните **Управление манифестом > Отправить манифест** и отправьте файл, который был обновлен на предыдущем шаге.

17. Оставьте эту страницу открытой, чтобы можно было копировать из и вставлять данные в нее и обновлять значения на странице при выполнении дальнейших шагов этого руководства.

## Настройка проекта ContactsList.Angular.AAD для вызова приложения API Azure

Следующие инструкции описывают, как развернуть и запустить приложение в Azure, но с небольшими изменениями вы можете запустить его и локально. Пример кода содержит конечные точки URL-адреса на локальном компьютере. Если вы хотите запустить приложение локально, настройте проект для использования протокола SSL, внесите в код проекта локальные URL-адреса с SSL и используйте локальные URL-адреса SSL в конфигурации приложения AAD. При локальном выполнении код на AngularJS будет разрешать вызов API только тем пользователям, которые вошли в систему. Но из других клиентов непроверенные пользователи смогут обращаться к API.

1. В проекте ContactsList.Angular.AAD откройте файл *app/scripts/app.js*.

8. В фрагменте кода, который задает переменную `endpoints`, закомментируйте конечную точку localhost и раскомментируйте конечную точку Azure.

10. Замените маркер yourclientid на фактическое значение идентификатора клиента для вашего приложения AAD, который можно узнать на классическом портале на вкладке **Настройка** приложения AAD.

2. Замените маркер {your api app name} на имя приложения API, в которое вы ранее развернули проект ContactsList.API.

	Код будет выглядеть примерно следующим образом:

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Кроме того, в *app.js* замените {your tenant url} и {your client id} в строке вызова `adalProvider.init` на фактические значения.

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

1. В файле *app/scripts/contactsSvc.js* измените URL-адрес localhost на URL-адрес приложения API, так же как и в *app.js*.

	Код будет выглядеть примерно следующим образом:

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";

## Настройка приложения AAD для веб-приложения Azure

1. На классическом портале на вкладке **Настройка** приложения AAD в поле **URL-адрес входа** удалите тот URL-адрес, который там указан, и замените его на базовый URL-адрес веб-приложения с косой чертой в конце. (Обратите внимание, что нужен URL-адрес веб-приложения, которое будет запускать код AngularJS, а не URL-адрес приложения API.)

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. В поле **URL-адрес ответа** замените URL-адрес, который там указан, на базовый URL-адрес веб-приложения.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Щелкните **Сохранить**.

## Развертывание проекта ContactsList.Angular.AAD в Azure

8. В **обозревателе решений** щелкните правой кнопкой мыши проект ContactsList.Angular.AAD и выберите пункт **Опубликовать**.

9. Щелкните **Служба приложений Microsoft Azure**.

10. В диалоговом окне **Служба приложений** в раскрывающемся списке **Подписка** выберите нужную подписку.

11. Разверните группу ресурсов, которая была создана для этого руководства, и выберите веб-приложение, созданное при работе со вторым руководством.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Нажмите кнопку **ОК**.

12. В мастере **веб-публикации** щелкните вкладку **Подключение**, а затем в поле **URL-адрес назначения** замените `http://` на `https://`.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

	Этот параметр определяет, какой URL-адрес будет открыт в обозревателе по умолчанию после успешного развертывания.

12. В мастере **веб-публикации** щелкните вкладку **Параметры**, разверните раздел **Параметры публикации файлов** и установите флажок **Удалить дополнительные файлы в месте назначения**.

7. Щелкните **Опубликовать**.

	Visual Studio развернет проект и откроет домашнюю страницу приложения в браузере.

## Тестирование веб-приложения AngularJS в Azure

8. Щелкните вкладку **Контакты**.

	Вам будет предложено войти.

9. Войдите с помощью учетных данных, используемых в клиенте AAD.

10. Появится страница **Контакты**.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

Внешний интерфейс теперь может вызвать API от имени пользователя, прошедшего проверку подлинности. Пользователи, не прошедшие проверку подлинности, не смогут вызвать API.

## Дальнейшие действия

В этом руководстве описывается ограничение доступа к приложению API с помощью проверки подлинности и авторизации службы приложений, которые позволяют вызывать его только пользователям, прошедшим проверку подлинности. В следующем руководстве этой серии вы узнаете, как [ограничить доступ к приложению API для сценариев взаимодействия служб](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0114_2016-->
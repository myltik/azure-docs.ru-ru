<properties
	pageTitle="Использование приложения API из JavaScript с помощью CORS | Microsoft Azure"
	description="Узнайте, как использовать приложение API в службе приложений Azure из клиента JavaScript с помощью CORS."
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
	ms.topic="get-started-article"
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# Использование приложения API из JavaScript с помощью CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Обзор

В этом руководстве показано, как использовать приложение API из кода JavaScript на веб-сайте, принадлежащем к другому домену. В примере клиента используется AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Это второе руководство из серии учебных материалов для работы с приложениями API в службе приложений Azure. Чтобы перейти к первому руководству из этой серии, выберите первый раздел в раскрывающемся списке **Раздел** вверху страницы.

## Поддержка CORS в службе приложений Azure

По соображениям безопасности в браузерах по умолчанию для JavaScript запрещены вызовы других доменов, помимо того, из которого выполняется JavaScript, с помощью API. Например, с веб-страницы contoso.com можно вызвать конечную точку API contoso.com, однако с той же веб-страницы нельзя вызвать конечную точку fabrikam.com. Cross Origin Resource Sharing (CORS) — это протокол IP, который позволяет осуществлять кросс-доменные вызовы API. В службе приложений Azure такие вызовы могут понадобиться, например, если клиент JavaScript выполняется в веб-приложении, а API запущен в приложении API.

Служба приложений Azure предоставляет простой способ настроить домены, которые могут вызывать приложение API, а функция CORS работает одинаково для всех языков, поддерживаемых службой приложений API, например Java и Node.js.

## Как работать с этим руководством

В этом руководстве используется пример приложения, который вы загрузили и из которого создали приложение API в [первом руководстве по версии ASP.NET из этой серии](app-service-api-dotnet-get-started.md). Если вы хотите работать с Java или Node.js, см. ниже [раздел о конфигурации CORS](#corsconfig), в котором приведены общие инструкции, применимые ко всем приложениям API.

## Пример проекта ContactsList.Angular

В [примере приложения ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) проект ContactsList.Angular представляет собой простой клиент AngularJS для проекта веб-API ContactsList.API.

В проекте ContactsList.Angular код JavaScript AngularJS, вызывающий API, находится в файле *index.html*. Этот код определяет функции и добавляет их в объект `$scope`, как показано в примере ниже, где метод GET API определяется как `$scope.refresh()`.

		angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
		    $scope.baseurl = 'http://localhost:51864';
		
		    $scope.refresh = function () {
		        $scope.status = "Refreshing Contacts...";
		        $http({
		            method: 'GET',
		            url: $scope.baseUrl + '/api/contacts',
		            headers: {
		                'Content-Type': 'application/json'
		            }
		        }).then(function (results) {
		            $scope.contacts = results.data;
		            $scope.status = "Contacts loaded";
		        }, function (err) {
		            $scope.status = "Error loading contacts";
		        });
		    };
		
		    // POST and DELETE not shown
		
		    $scope.refresh();
		});

Код вызывает метод $scope.refresh() при загрузке страницы (в конце фрагмента выше). Он привязан к кнопке **Обновить** в пользовательском интерфейсе.

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## Локальный запуск проекта AngularJS

В этом разделе вы узнаете, как проверить, можно ли запустить клиент локально, и вызвать с его помощью API, который также работает локально.

1. Задайте проекты ContactsList.API и ContactsList.Angular в качестве запускаемых проектов и задайте параметры, согласно которым сначала будет запускаться ContactsList.API, а затем —ContactsList.Angular. 

2. Нажмите клавишу F5, чтобы запустить проект.

	В пользовательском интерфейсе AngularJS отображаются локальные контакты. Здесь их можно добавлять и удалять.

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. Закройте окна браузера.

## Изменение проекта AngularJS для указания приложения API Azure 

Теперь необходимо запустить интерфейсную часть AngularJS в облаке и вызвать запущенную в нем внутреннюю службу API. Прежде чем развертывать интерфейсную часть в Azure, в проекте AngularJS необходимо изменить конечную точку API, чтобы код вызывал приложение Azure API, созданное ранее.

1. В проекте ContactsList.Angular откройте файл *index.html*.

2. Закомментируйте строку, которая задает `baseUrl` в качестве URL-адреса localhost, раскомментируйте строку, которая задает `baseUrl` в качестве URL-адреса azurewebsites.net, и замените заполнитель фактическим именем приложения API, которое вы создали ранее. Если вы назвали приложение API ContactsListAPI, теперь код выглядит следующим образом:

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### Развертывание проекта ContactsList.Angular в веб-приложении

Для развертывания проекта AngularJS можно создать отдельное веб-приложение, но в этом руководстве для развертывания используется веб-приложение, созданное в ходе работы с предыдущим руководством. В имени веб-приложения могут содержаться сведения о том, что изначально это приложение использовалось для развертывания проекта MVC ASP.NET, но после этого развертывания в нем будет выполняться код AngularJS.

8. В **обозревателе решений** щелкните проект ContactsList.Angular правой кнопкой мыши и выберите пункт **Опубликовать**.

9. Щелкните вкладку **Профиль**.

3.  В мастере **веб-публикации** на вкладке **Профиль** щелкните **Служба приложений Microsoft Azure**.

4. В диалоговом окне **Служба приложений** выберите необходимую подписку.

5. Задайте для параметра **Представление** значение по умолчанию **Группа ресурсов** и разверните группу ресурсов, созданную при работе с первым руководством этой серии.

7. Выберите веб-приложение, созданное при работе с первым руководством (не приложение API), и нажмите кнопку **ОК**.

8. Перейдите на вкладку **Параметры**.

9. Разверните раздел **Параметры публикации файлов** и установите флажок **Удалить дополнительные файлы в месте назначения**.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	Обычно при развертывании веб-проекта в существующее веб-приложение службы приложений не требуется устанавливать этот флажок, так как изменения — это, как правило, обновления и новые файлы. В этом случае вы развертываете в то же самое веб-приложение другой проект, поэтому, скорее всего, существует много файлов из более раннего развертывания, которые не нужны в новом развертывании.

10. Щелкните **Опубликовать**.

	Visual Studio развертывает проект ContactsList.Angular в веб-приложении и выполняет переход по URL-адресу веб-приложения в браузере. Появится тот же пользовательский интерфейс AngularJS, что и при локальном запуске, но при его работе произойдет сбой, так как интерфейсная часть запущена не в том домене (URL-адрес веб-приложения), где выполняется внутренняя служба (URL-адрес приложения API).

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## <a id="corsconfig"></a> Настройка CORS для целевого приложения API в Azure

8. В другом окне браузера перейдите на [портал Azure](https://portal.azure.com/).

9. Щелкните **Обзор > Приложения API**, а затем выберите целевое приложение API. В этом руководстве имеется в виду приложение API, созданное в первом руководстве для проекта ContactsList.API.

10. В колонке **Приложение API** выберите элемент **Параметры**.

11. В разделе **API** щелкните **CORS**.

12. В текстовое поле введите URL-адрес, с которого следует разрешить вызовы. Например, если вы развернули приложение JavaScript в веб-приложение с именем ContactsListMVC, введите http://contactslistmvc.azurewebsites.net.

	Обратите внимание, что вместо конкретного URL-адреса можно ввести символ звездочки (*). В таком случае будут приниматься вызовы из всех исходных доменов.

13. Щелкните **Сохранить**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Перейдите в окно обозревателя, в котором отображается клиент AngularJS, и обновите страницу или нажмите кнопку **Обновить**.

	Теперь на странице отображаются контакты, которые хранятся в файловой системе приложения API Azure.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

### CORS в инструментарии диспетчера ресурсов Azure

CORS для приложения API также можно настроить с помощью инструментария диспетчера ресурсов Azure. Для этого используются Azure PowerShell, интерфейс командной строки или [обозреватель ресурсов](https://resources.azure.com/).

Задайте свойство `cors` для типа Microsoft.Web/sites/config ресурса <site name>/web. Например, в **обозревателе ресурсов** последовательно выберите пункты **Подписки > {ваша подписка} > Группы ресурсов > {Ваша группа ресурсов} > Поставщики > Microsoft.Web > Сайты > {Ваш сайт} > Конфигурации > Веб**. Отобразится свойство cors:

		"cors": {
		    "allowedOrigins": [
		        "contactslistmvc.azurewebsites.net"
		    ]
		}

### CORS службы приложений и CORS веб-API

Для проектов веб-API ASP.NET можно легко настроить CORS в коде, как показано в следующем разделе. Но если CORS службы приложении и CORS веб-API используются совместно, CORS службы приложений будет иметь приоритет, а CORS веб-API — попросту игнорироваться. Например, если указать один исходный домен в службе приложений и все исходные домены в коде веб-API, приложение API Azure будет принимать вызовы только из домена, указанного в Azure.


## Настройка CORS в коде веб-API

В проекте веб-API можно установить пакет NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/), который позволяет указать в коде домены, из которых API будет принимать вызовы JavaScript. Этот процесс подробно описан в статье [Включение запросов независимо от источника в веб-API ASP.NET 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api). Для приложений API, созданных с использованием веб-API ASP.NET, процесс идентичен и кратко описан в этом разделе.

1. В проекте веб-API включите строку кода `config.EnableCors()` в метод **Register** класса **WebApiConfig**, как показано в следующем примере. 

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // The following line enables you to control CORS by using Web API code
				config.EnableCors();
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. В контроллере веб-API добавьте атрибут `EnableCors` в класс `ContactsController` или в отдельные методы действия. В следующем примере поддержка CORS действует для всего контроллера.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	> **Примечание.** Подстановочные знаки для всех параметров с атрибутом `EnableCors` используются только для демонстрации. Это откроет API для всех источников и всех HTTP-запросов. Этот атрибут следует использовать с осторожностью.

## Дальнейшие действия 

В этом руководстве показано, как включить поддержку CORS службы приложений, чтобы вызывать API-интерфейс, расположенный в другом домене, с помощью клиентского кода JavaScript. Из следующей статьи серии, посвященной началу работы с приложениями API, вы узнаете о [проверке подлинности для приложений API службы приложений](app-service-api-authentication.md).

<!---HONumber=AcomDC_1210_2015-->
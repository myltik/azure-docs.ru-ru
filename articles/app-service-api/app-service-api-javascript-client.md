<properties 
	pageTitle="Доступ к приложению API Azure с помощью HTML и JavaScript" 
	description="Узнайте, как получить доступ к серверной части приложения API с помощью HTML и JavaScript." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="07/31/2015" 
	ms.author="bradygaster"/>

# Использование приложения API Azure с помощью HTML и JavaScript

## Обзор

В этой статье показано, как создать клиент HTML и JavaScript для [приложения API](app-service-api-apps-why-best-platform.md) в [службе приложений Azure](/documentation/services/app-service/). В этой статье предполагается наличие практических навыков работы с HTML и JavaScript и будет рассмотрено использование платформы JavaScript [AngularJS](https://angularjs.org/) для осуществления вызовов REST приложения API.

Несколько статей для ознакомления, которые помогут вам приступить к работе.

1. [Создание приложения API](app-service-dotnet-create-api-app.md) — создание проекта приложения API.
2. [Развертывание приложения API](app-service-dotnet-deploy-api-app.md) — развертывание приложения API в подписке Azure.
3. [Отладка приложения API](../app-service-dotnet-remotely-debug-api-app.md) — использование Visual Studio для удаленной отладки кода во время его выполнения в Azure.

Данная статья основывается на указанных выше статьях, демонстрируя, как HTML-приложения могут использовать JavaScript для доступа к серверной части приложения API.

## Включение CORS

Как правило, CORS (доступ к ресурсам независимо от источника) необходим в HTML-приложениях, которые будут обслуживаться с узлов, отличных от API. Для приложений API существует по крайней мере два варианта включения CORS. В этом разделе рассказывается о них.

### Включение CORS для шлюзов приложения API

Шлюзы приложения API можно настроить для включения CORS с помощью портала предварительной версии Azure. Добавив параметр *appSetting* **MS_CrossDomainOrigins**, можно указать, каким URL-адресам разрешено вызывать приложение API. В данном разделе объясняется, как использовать этот параметр *appSetting* для включения CORS на уровне API шлюза.

1. На портале предварительной версии Azure перейдите к колонке приложения API, для которого необходимо разрешить CORS. Щелкните значок *Шлюз* для нужного приложения API. 

	![Нажатие кнопки шлюза приложения API](./media/app-service-api-javascript-client/19-api-app-blade.png)

1. Щелкните ссылку **Узел шлюза** в колонке портала.

	![Нажатие ссылки размещения шлюза приложения API](./media/app-service-api-javascript-client/20-gateway-host-blade.png)

1. Щелкните ссылку **Все параметры** в колонке портала.

	![Ссылка «Все параметры» шлюза](./media/app-service-api-javascript-client/21-gateway-blade-all-settings.png)

1. Нажмите кнопку **Параметры приложения** в колонке портала.

	![Параметры приложения шлюза](./media/app-service-api-javascript-client/22-gateway-app-settings-blade.png)

1. Добавьте параметр приложения **MS_CrossDomainOrigins**. В качестве значения параметра используйте разделенный запятыми список узлов HTTP, которым нужно предоставить доступ к приложению API. Если необходимо предоставить доступ к нескольким узлам, можно в качестве значения параметра *appSetting* использовать, например, следующий код.

		http://foo.azurewebsites.net, https://foo.azurewebsites.net, http://contactlistwebapp.azurewebsites.net

	В следующем разделе рассказывается о создании отдельного веб-приложения, содержащего простую HTML-страницу, которая вызывает приложение API из веб-приложения, запущенного в той же группе ресурсов Azure, что и приложение API. Поскольку это единственный узел, которому будет разрешен доступ к приложению API, будет задано значение, содержащее один узел.

		http://contactlistwebapp.azurewebsites.net

	На приведенном ниже снимке экрана показано, как этот параметр должен выглядеть после его сохранения на портале предварительной версии Azure.

	![](./media/app-service-api-javascript-client/23-app-settings-set.png)

Параметр приложения **MS_CrossDomainOrigins** подробно описан в записи блога [Обновления .NET мобильной службы Azure](http://azure.microsoft.com/blog/2014/07/28/azure-mobile-services-net-updates/), где можно найти более подробные сведения о параметре.

### Включение CORS в коде веб-API

Процесс включения CORS в веб-API подробно описан в статье ASP.NET [Включение запросов независимо от источника в веб-API 2 ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api). Для приложений API, созданных с использованием веб-API ASP.NET, процесс идентичен и будет кратко описан далее. Если CORS уже включен, пропустите этот раздел, т. к. приложение API уже должно быть настроены правильно.

1. Функциональные возможности CORS предоставляются пакетом NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/). Чтобы его установить, откройте **консоль диспетчера пакетов** и выполните следующий сценарий PowerShell. 

		Install-Package Microsoft.AspNet.WebApi.Cors

1. После выполнения команды в консоли диспетчера пакетов и **packages.config** будет отображен дополнительный новый пакет NuGet.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/01-cors-installed.png)

1. Откройте файл *App_Start/WebApiConfig.cs*. Добавьте следующую строку кода в метод **Register** класса **WebApiConfig** в файле.

		config.EnableCors();

	После обновления файла код должен выглядеть следующим образом:

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
				config.EnableCors(); /* -- NEW CODE -- */
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. Последним шагом для включения CORS является обозначение отдельных методов действий, которые требуется включить. Добавьте атрибут **EnableCors** для каждого метода или для всего контроллера, как показано в приведенном ниже коде.

	> **Примечание**. Подстановочные знаки для всех параметров с атрибутом EnableCors используются только для демонстрации. Это откроет API для всех источников и всех HTTP-запросов. Этот атрибут следует использовать с осторожностью и понимать последствия.

		using ContactList.Models;
		using System.Collections.Generic;
		using System.Web.Http;
		using System.Web.Http.Cors;
	
		namespace ContactList.Controllers
		{
		    [EnableCors(origins:"*", headers:"*", methods: "*")] /* -- NEW CODE -- */
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]
		            {
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		
		        [HttpPost]
		        public Contact Post([FromBody] Contact contact)
		        {
		            return contact;
		        }
		    }
		}
1. Если приложение API уже развернуто в Azure перед добавлением поддержки CORS, повторно разверните код, чтобы CORS был включен в API, размещенном в Azure. 

## Создание веб-приложения для использования приложения API

В этом разделе создается новое пустое веб-приложение, устанавливается и используется AngularJS в нем и устанавливается связь простого внешнего интерфейса HTML с приложением API. Будет выполнено развертывание используемого веб-приложения в службу приложений Azure. Веб-приложение HTML установит связь с данными, полученными из приложения API, и будет отображать их, а также обеспечит пользователям простой пользовательский интерфейс для API контактов.

1. Щелкните правой кнопкой мыши решение, которое было создано ранее, как описано в статье [Создание приложения API](app-service-dotnet-create-api-app.md), и выберите **Добавить -> Новый проект**.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/02-add-project.png)

1. Выберите шаблон **Веб-приложение ASP.NET**.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/03-new-web-project.png)

1. В диалоговом окне One ASP.NET выберите шаблон **Пустой**.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/04-empty-web.png)

1. С помощью **Диспетчера пакетов** или элемента контекстное меню **Управление пакетами NuGet** установите пакет NuGet [AngularJS](https://www.nuget.org/packages/angularjs).

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/05-install-angular.png)

1. С помощью **Диспетчера пакетов** или элемента контекстное меню **Управление пакетами NuGet** установите пакет NuGet [Bootstrap](https://www.nuget.org/packages/bootstrap).

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/05-install-bootstrap.png)

1. Добавьте новый HTML-файл в проект веб-приложения.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/06-new-html-file.png)

1. Присвойте файлу имя *index.html*.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/07-index-html.png)

1. Добавьте на HTML-страницу файлы начальной загрузки CSS и AngularJS JavaScript, а также используйте простой шаблон Bootstrap ([типа этого](http://getbootstrap.com/examples/starter-template/)) и создайте тег пустого сценария для подготовки страницы.
	
	> Примечание. Комментарии в приведенном ниже коде HTML и JavaScript являются предварительными элементами для последующих шагов в этом разделе.

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Contacts HTML Client</title>
		    <link href="Content/bootstrap.css" rel="stylesheet" />
		    <style type="text/css">
		        body {
		            margin-top: 60px;
		        }
		    </style>
		</head>
		<body>
		
		    <nav class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
		                    <span class="sr-only">Toggle navigation</span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                <a class="navbar-brand" href="#">Contacts</a>
		            </div>
		            <div id="navbar" class="collapse navbar-collapse">
		                <ul class="nav navbar-nav"></ul>
		            </div>
		        </div>
		    </nav>
		
		    <div class="container">
		        <!-- contacts ui here -->
		    </div>
		
		    <script src="Scripts/angular.js" type="text/javascript"></script>
		    <script type="text/javascript">
		        /* client javascript code here */
		    </script>
		
		</body>
		</html>

1. Добавьте приведенный ниже код таблицы HTML к элементу *div* **контейнер** в HTML.

		<!-- contacts ui here -->
        <table class="table table-striped" ng-app="myApp" ng-controller="contactListCtrl">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="con in contacts">
                    <td>[[con.Id]]</td>
                    <td>[[con.Name</td>
                    <td>[[con.EmailAddress]]</td>
                    <td></td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th>Create a new Contact</th>
                    <th colspan="2">API Status: [[status]]</th>
                    <th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>
                </tr>
                <tr>
                    <td><input type="text" placeholder="ID" ng-model="newId" /></td>
                    <td><input type="text" placeholder="Name" ng-model="newName" /></td>
                    <td><input type="text" placeholder="Email Address" ng-model="newEmail" /></td>
                    <td><button class="btn btn-sm btn-success" ng-click="create()">Create</button></td>
                </tr>
            </tfoot>
        </table>

1. В элементах `tbody` и `tfoot` замените каждый знак [ на {, а каждый знак] — на }. (В настоящее время на этом сайте невозможно отобразить двойные фигурные скобки в блоках кода.)

2. Щелкните правой кнопкой мыши файл *index.html* и выберите **Задание в качестве начальной страницы**.

3. Щелкните правой кнопкой мыши файл *index.html* и выберите **Просмотр в браузере**.

	Обратите внимание на заголовки шаблонов в выходных данных HTML. На следующем шаге будет выполнена привязка данных этих элементов HTML с помощью AngularJS.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/09-template-ui.png)

1. Добавьте приведенный ниже код JavaScript в файл *index.html* для вызова API и выполните привязку данных пользовательского интерфейса HTML к выходным данным API.

		/* client javascript code here */
        angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
            $scope.baseUrl = 'http://localhost:1578';

            $scope.refresh = function () {
                $scope.status = "Refreshing Contacts...";
                $http({
                    method: 'GET',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    }
                }).success(function (data) {
                    $scope.contacts = data;
                    $scope.status = "Contacts loaded";
                }).error(function (data, status) {
                    $scope.status = "Error loading contacts";
                });
            };

            $scope.create = function () {
                $scope.status = "Creating a new contact";

                $http({
                    method: 'POST',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        'Id': $scope.newId,
                        'Name': $scope.newName,
                        'EmailAddress': $scope.newEmail
                    }
                }).success(function (data) {
                    $scope.status = "Contact created";
                    $scope.refresh();
                    $scope.newId = 0;
                    $scope.newName = '';
                    $scope.newEmail = '';
                });
            };

            $scope.refresh();
        });

1. В коде, который вы добавили в файл index.html, замените номер порта в базовом URL-адресе (`http://localhost:1578`) на фактический номер порта для проекта API.

	> **Примечание**: не используйте номер порта, применяемый в проекте создания HTML-клиента. Чтобы открыть окно браузера с номером порта, щелкните правой кнопкой мыши проект API, затем выберите пункт Отладка > Начать новый экземпляр.

1. Убедитесь, что проект приложения API также выполняется при запуске клиента HTML, иначе JavaScript HTML будет работать неправильно. Щелкните правой кнопкой мыши решение и выберите элемент **Свойства**. Затем установите для обоих веб-проектов значение **Запуск без отладки**, а также параметр, в соответствии с которым сначала запускается проект API. 

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/10-run-both-web-projects.png)

1. Запустите решение, и клиент HTML или JavaScript подключится к данным из проекта приложения API и будет их отображать.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/11-web-client-running.png)

## Развертывание веб-приложения

В этом разделе будет выполнено развертывание клиента HTML/JavaScript как веб-приложение службы приложений. После завершения развертывания вы измените URL-адрес, используемый JavaScript в развернутом приложении API.

> Примечание. В этом разделе предполагается, что вы прочитали статью [Развертывание приложения API](app-service-dotnet-deploy-api-app.md) и выполнили описанные в ней действия или развернули свое приложение API ранее.

1. Откройте колонку приложения API на портале предварительной версии Azure. Щелкните в колонке URL-адрес, чтобы открыть его в браузере. Когда он откроется, скопируйте URL-адрес приложения API из адресной строки браузера. 

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/12-open-api-app-from-blade.png)

1. Вставьте URL-адрес приложения API, перезаписав предыдущее значение свойства **$scope.baseUrl** в коде JavaScript.

		$scope.baseUrl = 'https://microsoft-apiappf7e042ba8e5233ab4312021d2aae5d86.azurewebsites.net';

	Обратите внимание, что URL-адрес указывает протокол HTTPS. Использование протокола HTTPS является обязательным, так как приложения API не поддерживают HTTP.

1. Щелкните правой кнопкой мыши веб-проект HTML/JavaScript и выберите пункт контекстного меню **Опубликовать**.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/13-publish-web-app.png)

1. В диалоговом окне «Публикация в Интернете» выберите элемент **Веб-приложения Microsoft Azure**.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/14-publish-web-dialog.png)

1. Щелкните кнопку **Создать**, чтобы создать новое веб-приложение.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/15-new-web-app.png)

1. Выберите тот же план размещения приложения и группу ресурсов, в которой уже запущено ваше приложение API.

	> **Примечание**. Это не является обязательным, но для демонстрационных целей размещение всех элементов в одной группе ресурсов упрощает очистку ресурсов Azure в дальнейшем.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/16-new-web-app-creation-dialog.png)

1. Выполните действия для веб-публикации, чтобы развернуть клиент HTML/JavaScript в службе веб-приложений.
1. После развертывания веб-приложения оно должно автоматически открыться в вашем веб-браузере и отобразить данные из приложения API. 

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/17-web-app-running-in-ie.png)

1. На этом этапе при переходе к группе ресурсов вы увидите, что новое веб-приложение работает параллельно с приложением API.

	![apiapp.json и метаданные в обозревателе решений](./media/app-service-api-javascript-client/18-web-app-visible-in-resource-group.png)

## Дальнейшие действия 

В этом примере показано, как использовать AngularJS в качестве платформы JavaScript для доступа к серверной части приложений API. Можно изменить функции доступа REST и использовать любую другую платформу JavaScript.

В этом примере показан доступ к приложению API без проверки подлинности. Сведения о проверке подлинности в службе приложений см. в статье [Проверка подлинности для приложений API и мобильных приложений](../app-service/app-service-authentication-overview.md).

<!---HONumber=August15_HO6-->
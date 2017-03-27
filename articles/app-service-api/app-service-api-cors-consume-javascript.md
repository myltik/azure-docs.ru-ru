---
title: "Поддержка CORS в службе приложений | Документация Майкрософт"
description: "Узнайте, как использовать поддержку CORS в службе приложений Azure."
services: app-service\api
documentationcenter: .net
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 4f980a97-b9f5-4d1d-87ab-82b60bb96e1c
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/27/2016
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 79fe018bb6721c431a935dda14b36968688d34e3
ms.lasthandoff: 03/21/2017


---
# <a name="consume-an-api-app-from-javascript-using-cors"></a>Использование приложения API из JavaScript с помощью CORS
В службе приложений реализована встроенная поддержка технологии [общего доступа к ресурсам независимо от источника (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing), что позволяет клиентам JavaScript выполнять междоменные вызовы к API, размещенным в приложениях API. С помощью службы приложений можно настроить доступ CORS без написания кода в API.

Эта статья содержит два раздела.

* В разделе [Настройка CORS](#corsconfig) описывается общая настройка CORS для любого приложения API, веб-приложения или мобильного приложения. Инструкции из этого раздела применимы ко всем платформам, которые поддерживает служба приложений, включая .NET, Node.js и Java. 
* Начиная с раздела [Продолжение руководства по началу работы с .NET](#tutorialstart) в статье описывается использование CORS. Для работы с этой частью необходимо выполнить инструкции, приведенные [в первом руководстве по началу работы с приложениями API](app-service-api-dotnet-get-started.md). 

## <a id="corsconfig"></a> Настройка CORS в службе приложений Azure
CORS можно настроить на портале Azure или с помощью средств [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

#### <a name="configure-cors-in-the-azure-portal"></a>Настройка CORS на портале Azure
1. В браузере перейдите на [портал Azure](https://portal.azure.com/).
2. Щелкните **Службы приложений**и выберите имя приложения API.
   
    ![Выбор приложения API на портале](./media/app-service-api-cors-consume-javascript/browseapiapps.png)
3. В колонке **Параметры**, расположенной справа от колонки **Приложение API**, найдите раздел **API** и щелкните элемент **CORS**.
   
   ![Выбор CORS в колонке параметров](./media/app-service-api-cors-consume-javascript/clicksettings.png)
4. В текстовое поле введите URL-адреса, с которых следует разрешить вызовы JavaScript.

    Например, если вы развернули приложение JavaScript в веб-приложении с именем todolistangular, введите https://todolistangular.azurewebsites.net. Вместо URL-адресов можно ввести символ звездочки (*). В таком случае будут приниматься вызовы из всех исходных доменов.


1. Щелкните **Сохранить**.
   
   ![Щелкните Сохранить](./media/app-service-api-cors-consume-javascript/corsinportal.png)
   
   После нажатия кнопки **Сохранить**приложение API начнет принимать вызовы JavaScript с указанных URL-адресов.

#### <a name="configure-cors-by-using-azure-resource-manager-tools"></a>Настройка CORS с помощью средств диспетчера ресурсов Azure
CORS для приложения API можно также настроить с помощью [шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md), используя такие программы командной строки, как [Azure PowerShell](/powershell/azureps-cmdlets-docs) и [интерфейс командной строки Azure](../cli-install-nodejs.md). 

Чтобы просмотреть пример шаблона Azure Resource Manager, который задает свойство CORS, откройте [в репозитории файл azuredeploy.json, пример приложения для этого руководства](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Найдите раздел шаблона, который выглядит так:

        "cors": {
            "allowedOrigins": [
                "todolistangular.azurewebsites.net"
            ]
        }

## <a id="tutorialstart"></a> Продолжение руководства по началу работы с .NET
Если вы изучаете серию руководств для приложений API по началу работы с Node.js или Java, на этом этапе данная серия заканчивается. Перейдите к разделу [Дальнейшие действия](#next-steps) , в котором предложены дополнительные статьи о приложениях API.

Оставшаяся часть статьи — это продолжение серии руководств по началу работы с .NET, для работы с которой вам нужно сначала выполнить инструкции, приведенные в [первом руководстве](app-service-api-dotnet-get-started.md).

## <a name="deploy-the-todolistangular-project-to-a-new-web-app"></a>Развертывание проекта ToDoListAngular в новом веб-приложении
Следуя инструкциям из [первого руководства](app-service-api-dotnet-get-started.md), вы создали приложение API среднего уровня и приложение API уровня данных. Изучив это руководство, вы создадите одностраничное веб-приложение (SPA), которое вызывает приложение API среднего уровня. Для работы SPA следует включить поддержку CORS в приложении API среднего уровня. 

В [примере приложения ToDoList](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list)проект ToDoListAngular является простым клиентом AngularJS, который используется для вызова проекта веб-API среднего уровня ToDoListAPI. Код JavaScript в файле *app/scripts/todoListSvc.js* вызывает API с использованием поставщика HTTP AngularJS. 

        angular.module('todoApp')
        .factory('todoListSvc', ['$http', function ($http) {

            $http.defaults.useXDomain = true;
            delete $http.defaults.headers.common['X-Requested-With']; 

            return {
                getItems : function(){
                    return $http.get(apiEndpoint + '/api/TodoList');
                },

                /* Get by ID, Put, and Delete methods not shown */

                postItem : function(item){
                    return $http.post(apiEndpoint + '/api/TodoList', item);
                }
            };
        }]);

### <a name="create-a-new-web-app-for-the-todolistangular-project"></a>Создание нового веб-приложения для проекта ToDoListAngular
Процедура создания веб-приложения службы приложений и развертывания в нем проекта похожа [на аналогичную процедуру для приложения API в первом руководстве этой серии](app-service-api-dotnet-get-started.md#createapiapp). Разница только в том, что приложение относится к типу **Веб-приложение**, а не **Приложение API**.  Снимки экрана диалоговых окон можно просмотреть, выполнив следующее. 

1. В **обозревателе решений** щелкните правой кнопкой мыши проект ToDoListAngular и выберите пункт **Опубликовать**.
2. В мастере **Публикация веб-сайта** на вкладке **Профиль** щелкните **Служба приложений Microsoft Azure**.
3. В диалоговом окне **Служба приложений** нажмите кнопку **Создать**.
4. На вкладке **Размещение** в диалоговом окне **Создание службы приложений** укажите **имя веб-приложения** (оно должно быть уникальным в домене *azurewebsites.net*). 
5. В поле **Подписка** выберите подписку Azure, которую нужно использовать.
6. В раскрывающемся списке **Группа ресурсов** выберите созданную ранее группу ресурсов.
7. В раскрывающемся списке **План службы приложений** выберите созданный ранее план. 
8. Щелкните **Создать**.
   
    В Visual Studio создается веб-приложение и профиль публикации для него, а также отображается шаг **Подключение** мастера **веб-публикации**.
   
    Пока не нажимайте кнопку **Опубликовать** . В следующем разделе вы настроите новое веб-приложение, чтобы вызвать приложение API среднего уровня, которое выполняется в службе приложений. 

### <a name="set-the-middle-tier-url-in-web-app-settings"></a>Указание URL-адреса среднего уровня в параметрах веб-приложения
1. На [портале Azure](https://portal.azure.com/)откройте колонку **Веб-приложение** веб-приложения, созданного для размещения проекта ToDoListAngular (внешнее приложение).
2. Щелкните элементы **Параметры > Параметры приложения**.
3. В разделе **Параметры приложения** добавьте следующий ключ и значение:
   
   | Ключ | Значение | Пример |
   | --- | --- | --- |
   | toDoListAPIURL |https://{имя_приложения_API_среднего_уровня}.azurewebsites.net |https://todolistapi0121.azurewebsites.net |
4. Щелкните **Сохранить**.
   
    Если код выполняется в среде Azure, это значение теперь будет переопределять URL-адрес локального узла, который находится в файле *Web.config* . 
   
    Код, который получает значение параметра, находится в файле *index.cshtml*:
   
        <script type="text/javascript">
            var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
        </script>
        <script src="app/scripts/todoListSvc.js"></script>
   
    Код в файле *todoListSvc.js* использует этот параметр:
   
        return {
            getItems : function(){
                return $http.get(apiEndpoint + '/api/TodoList');
            },
            getItem : function(id){
                return $http.get(apiEndpoint + '/api/TodoList/' + id);
            },
            postItem : function(item){
                return $http.post(apiEndpoint + '/api/TodoList', item);
            },
            putItem : function(item){
                return $http.put(apiEndpoint + '/api/TodoList/', item);
            },
            deleteItem : function(id){
                return $http({
                    method: 'DELETE',
                    url: apiEndpoint + '/api/TodoList/' + id
                });
            }
        };

### <a name="deploy-the-todolistangular-web-project-to-the-new-web-app"></a>Развертывание веб-проекта ToDoListAngular в новом веб-приложении
* В Visual Studio на шаге **Подключение** мастера **веб-публикации** нажмите кнопку **Опубликовать**.
  
   Visual Studio развернет проект ToDoListAngular в новое веб-приложение и откроет в браузере URL-адрес веб-приложения. 

### <a name="test-the-application-without-cors-enabled"></a>Тестирование приложения с выключенным механизмом CORS
1. В средствах разработчика браузера откройте окно консоли.
2. В окне браузера с пользовательским интерфейсом AngularJS щелкните ссылку **To Do List** .
   
    Код JavaScript попытается вызвать приложение API среднего уровня. Вызов завершится сбоем, так как внешнее приложение выполняется в домене, который отличается от домена внутреннего приложения. В окне браузера "Консоль средств разработчика" сообщение об ошибке отображается независимо от источника.
   
    ![Сообщение об ошибке CORS](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## <a name="configure-cors-for-the-middle-tier-api-app"></a>Настройка CORS для приложения API среднего уровня
В этом разделе рассматривается настройка параметра CORS в Azure для приложения API среднего уровня ToDoListAP. Этот параметр позволит приложению API среднего уровня получать вызовы JavaScript из веб-приложения, созданного для проекта ToDoListAngular.

1. В браузере перейдите на [портал Azure](https://portal.azure.com/).
2. Щелкните **Службы приложений**, а затем выберите приложение API ToDoListAPI (среднего уровня).
   
    ![Выбор приложения API на портале](./media/app-service-api-cors-consume-javascript/browseapiapps.png)
3. В колонке **Параметры**, расположенной справа от колонки **Приложение API**, найдите раздел **API** и щелкните элемент **CORS**.
   
   ![Выбор CORS на портале](./media/app-service-api-cors-consume-javascript/clicksettings.png)
4. Введите в текстовое поле URL-адрес веб-приложения ToDoListAngular (внешнее приложение). Например, если вы развернули проект ToDoListAngular в веб-приложении с именем todolistangular0121, разрешите вызовы с URL-адреса `https://todolistangular0121.azurewebsites.net`.
   
   Вместо URL-адресов можно ввести символ звездочки (*). В таком случае будут приниматься вызовы из всех исходных доменов.
5. Щелкните **Сохранить**.
   
   ![Щелкните Сохранить](./media/app-service-api-cors-consume-javascript/corsinportal.png)
   
   Когда вы нажмете кнопку **Сохранить**, приложение API начнет принимать вызовы JavaScript с указанного URL-адреса. Из этого снимка экрана видно, что приложение API ToDoListAPI0223 будет принимать клиентские вызовы JavaScript из веб-приложения ToDoListAngular.

### <a name="test-the-application-with-cors-enabled"></a>Тестирование приложения с включенным механизмом CORS
* Откройте в браузере URL-адрес HTTPS своего веб-приложения. 
  
    На этот раз в приложении можно просматривать, добавлять, изменять и удалять элементы списка дел. 
  
    ![Страница To Do List в примере приложения](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## <a name="app-service-cors-versus-web-api-cors"></a>CORS службы приложений и CORS веб-API
В проекте веб-API можно установить пакет NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/). Так вы сможете указывать в коде домены, из которых API будет принимать вызовы JavaScript.

Поддержка CORS веб-API более гибкая, чем поддержка CORS службы приложений. Например, в коде можно указать различные принимаемые источники для различных методов действий, тогда как для CORS службы приложений указывается один набор принимаемых источников для всех методов приложения API.

> [!NOTE]
> Не пытайтесь использовать CORS веб-API и CORS службы приложений в одном приложении API. В таком случае будет использоваться CORS службы приложений, а CORS веб-API — попросту игнорироваться. Например, если указать один исходный домен в службе приложений и все исходные домены в коде веб-API, приложение API Azure будет принимать вызовы только из домена, указанного в Azure.
> 
> 

### <a name="how-to-enable-cors-in-web-api-code"></a>Включение CORS в коде веб-API
Ниже кратко описано, как включить поддержку CORS веб-API. Дополнительные сведения см. в статье [Enabling Cross-Origin Requests in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) (Включение запросов независимо от источника в веб-API 2 ASP.NET).

1. В проекте веб-API установите пакет NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) .
2. Включите строку кода `config.EnableCors()` в метод **Register** класса **WebApiConfig**, как показано в следующем примере. 
   
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
3. В контроллере веб-API добавьте инструкцию `using` для пространства имен `System.Web.Http.Cors`, а также добавьте атрибут `EnableCors` в класс контроллера или отдельные методы действий. В следующем примере поддержка CORS действует для всего контроллера.
   
        namespace ToDoListAPI.Controllers 
        {
            [HttpOperationExceptionFilterAttribute]
            [EnableCors(origins:"https://todolistangular0121.azurewebsites.net", headers:"accept,content-type,origin,x-my-header", methods: "get,post")]
            public class ToDoListController : ApiController

## <a name="using-azure-api-management-with-api-apps"></a>Использование службы управления API с приложениями API
Если вы используете службу управления API Azure с приложением API, настраивайте CORS в службе управления API, а не в приложении API. Для получения дополнительных сведений см. следующие ресурсы:

* [Azure API Management Overview](https://azure.microsoft.com/documentation/videos/azure-api-management-overview/) (Видеообзор службы управления API Azure). Часть о CORS начинается с 12:10 мин.
* [API Management cross domain policies](https://msdn.microsoft.com/library/azure/dn894084.aspx#CORS)

## <a name="troubleshooting"></a>Устранение неполадок
Если в ходе выполнения инструкций этого руководства возникнут проблемы, можно воспользоваться рекомендациями по устранению неполадок ниже.

* Убедитесь, что используется последняя версия пакета [Azure SDK для .NET для Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003).
* Проверьте, что в параметре CORS указан протокол `https`, а также что для запуска внешнего веб-приложения используется протокол `https`.
* Убедитесь, что вы ввели параметр CORS в приложении API среднего уровня, а не во внешнем веб-приложении.
* Если вы настраиваете CORS в коде приложения и службе приложений Azure, учтите, что параметр CORS службы приложений переопределит все изменения в коде приложения. 

Дополнительные сведения о функциях Visual Studio, которые упрощают устранение неполадок, см. в статье [Устранение неполадок веб-приложения в службе приложений Azure с помощью Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье показано, как включить поддержку CORS в службе приложений, чтобы вызывать приложение API, выполняющееся в другом домене, с помощью клиентского кода JavaScript. Дополнительные сведения о приложениях API см. в руководствах по [введению в проверку подлинности службы приложений](../app-service/app-service-authentication-overview.md) и [проверке подлинности пользователя для приложений API](app-service-api-dotnet-user-principal-auth.md).



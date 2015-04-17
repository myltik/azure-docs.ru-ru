<properties 
	pageTitle="Создание приложения API службы Azure" 
	description="В этой статье показано, как использовать Visual Studio 2013 для создания приложения API в службе приложений Azure" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Создание приложения API в службе приложений Azure

## Обзор

Это первый из трех учебников серии, который поможет начать работу с приложениями API в службе приложений Azure.

1. В этом учебнике вы создадите новое приложение API и подготовите его к развертыванию в подписке Azure. 
* В учебнике [Развертывание приложения API](app-service-dotnet-create-api-app.md) вы развернете приложение API в подписке Azure.
* В учебнике [Отладка приложения API](app-service-dotnet-remotely-debug-api-app.md) вы используете Visual Studio для удаленной отладки кода во время его выполнения в Azure.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Создание первого приложения API ##

Откройте Visual Studio 2013 и последовательно щелкните пункты **Файл > Создать проект**. Выберите шаблон **Веб-приложение ASP.NET**.  Присвойте проекту имя *ContactsList*, а затем щелкните **ОК**.

![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

Выберите шаблон проекта **Приложение API Azure** и нажмите кнопку **ОК**.

![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

Щелкните правой кнопкой мыши папку **Модели** в проекте веб-API, а затем в контекстном меню последовательно выберите пункты **Добавить > Класс**. 

![](./media/app-service-dotnet-create-api-app/03-add-new-class-v3.png) 

Назовите новый файл *Contact.cs* и щелкните **Добавить**. 

![](./media/app-service-dotnet-create-api-app/0301-add-new-class-dialog-v3.png) 

Замените все содержимое файла следующим кодом. 

	namespace ContactsList.Models
	{
		public class Contact
		{
			public int Id { get; set; }
			public string Name { get; set; }
			public string EmailAddress { get; set; }
		}
	}

Щелкните правой кнопкой мыши папку **Контроллеры**, а затем в контекстном меню последовательно выберите пункты **Добавить > Контроллер**. 

![](./media/app-service-dotnet-create-api-app/05-new-controller-v3.png)

В диалоговом окне **Добавление функции формирования шаблонов** выберите параметр **Контроллер веб-API 2 - пустой** и щелкните **Добавить**. 

![](./media/app-service-dotnet-create-api-app/06-new-controller-dialog-v3.png)

Назовите контроллер **ContactsController** и щелкните **Добавить**. 

![](./media/app-service-dotnet-create-api-app/07-new-controller-name-v2.png)

Замените код в этом файле кодом ниже. 

	using ContactsList.Models;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Net;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Web.Http;
	
	namespace ContactsList.Controllers
	{
	    public class ContactsController : ApiController
	    {
	        [HttpGet]
	        public IEnumerable<Contact> Get()
	        {
	            return new Contact[]{
					new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
					new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                	new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}

В проекты приложений API входит автоматическое создание метаданных [Swagger](http://swagger.io/ "Official Swagger information") и тестовая страница API. По умолчанию тестовая страница API отключена. Чтобы включить тестовую страницу API, откройте файл *App_Start/SwaggerConfig.cs*. Найдите **EnableSwaggerUI**:

![](./media/app-service-dotnet-create-api-app/12-enable-swagger-ui-with-box.png)

Раскомментируйте следующие строки кода:

        })
    .EnableSwaggerUi(c =>
        {

После этого файл должен выглядеть в Visual Studio 2013 следующим образом.

![](./media/app-service-dotnet-create-api-app/13-enable-swagger-ui-with-box.png)

Чтобы просмотреть тестовую страницу API, запустите приложение локально и перейдите в каталог `/swagger`. 

![](./media/app-service-dotnet-create-api-app/14-swagger-ui.png)

Нажмите кнопку **Try it out** (Испытать). Вы увидите, что API работает и возвращает ожидаемый результат. 

![](./media/app-service-dotnet-create-api-app/15-swagger-ui-post-test.png)

## Дальнейшие действия

Теперь приложение API готово к развертыванию, и вы можете перейти к учебнику [Развертывание приложения API](app-service-dotnet-deploy-api-app.md).

Дополнительные сведения см. в разделе [Что такое приложения API?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->
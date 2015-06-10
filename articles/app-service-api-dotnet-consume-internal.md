<properties 
	pageTitle="Использование внутреннего приложения API в службе приложений Azure из клиента .NET" 
	description="Узнайте, как использовать приложение API из приложения API для .NET в одной группе ресурсов с помощью пакета SDK службы приложений." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Использование внутреннего приложения API в службе приложений Azure из клиента .NET 

## Обзор

В этом учебнике показано, как написать код для [приложения API](app-service-api-apps-why-best-platform.md) для ASP.NET, вызывающего другое приложение API, для которого настроен **Внутренний** уровень доступа. Оба приложения API должны быть в одной и той же группе ресурсов. Этот код также можно использовать для вызова внутреннего приложения API с помощью [мобильного приложения](app-service-mobile-value-prop-preview.md).

Далее вы создадите веб-API ContactNames. Метод Get веб-API вызовет приложение API ContactsList и возвратит только имена из контактных данных, указанных в приложении API ContactsList. Ниже представлен экран пользовательского интерфейса Swagger для успешного вызова метода Get приложения ContactNames.

![](./media/app-service-api-dotnet-consume-internal/tryitout.png)

Информацию о том, как вызывать приложения API, для которых настроен **Общедоступный (анонимный)** или **Общедоступный (аутентифицированный)** уровень доступа, см. в разделе [Использование приложения API в службе приложений Azure из клиента .NET](app-service-api-dotnet-consume.md).

## Предварительные требования

В этом учебнике предполагается, что вы знаете, как создавать проекты и добавлять в них код в Visual Studio, а также, как [управлять приложениями API на портале предварительной версии Azure](app-service-api-apps-manage-in-portal.md).

Проект и примеры кода в этой статье основаны на проекте приложения API, созданном и развернутом с использованием информации из таких статей:

- [Создание приложения API](app-service-dotnet-create-api-app.md)
- [Развертывание приложения API](app-service-dotnet-deploy-api-app.md)

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Для этого учебника требуется пакет SDK Azure для .NET версии 2.6 или более поздней.

### Настройка целевого приложения API

1. Если приложение еще не настроено, выполните действия, описанные в учебнике [Развертывание приложения API](app-service-dotnet-deploy-api-app.md), чтобы развернуть пример проекта ContactsList в приложение API в своей подписке Azure.

2. На [портале предварительной версии Azure](https://portal.azure.com/) в колонке **Приложение API** приложения API ContactsList, развернутого ранее, перейдите к разделу **Параметры > Параметры приложения**, установите для параметра **Уровень доступа** значение **Внутренний** и щелкните **Сохранить**.

	![](./media/app-service-api-dotnet-consume-internal/setinternal.png)
 
## Создание нового приложения API, которое будет вызывать имеющееся приложение API

- В Visual Studio создайте проект приложения API с именем ContactNames, используя шаблон проектов приложений API Azure.

	В процессе выполняются те же действия, что и в разделе [Создание приложения API](app-service-dotnet-create-api-app.md), но позже в этом учебнике вы добавите в проект другой код.
 
## Добавление кода клиента, созданного пакетом SDK службы приложений

Более подробное описание следующих шагов см. в разделе [Использование приложения API в службе приложений Azure из клиента .NET](app-service-api-dotnet-consume.md).

3. В **Обозревателе решений** щелкните правой кнопкой мыши проект (не решение) и выберите команду **Добавить > Клиент приложения API Azure**. 

3. В диалоговом окне **Добавление клиента приложения API Azure ** установите переключатель **Загрузить из приложения API Azure**.

5. В раскрывающемся списке выберите приложение API, которое необходимо вызвать. Для этого учебника выберите приложение API ContactsList, созданное ранее.

7. Нажмите кнопку **ОК**.

## Включение пользовательского интерфейса Swagger

По умолчанию для проектов приложений API включено автоматическое создание метаданных [Swagger](http://swagger.io/ "Официальные сведения о Swagger"), но шаблон нового проекта приложения API Azure отключает тестовую страницу API. В этом разделе вы включите эту тестовую страницу.

1. Откройте файл *App_Start/SwaggerConfig.cs* и найдите **EnableSwaggerUI**:

2. Раскомментируйте следующие строки кода:

	        })
	    .EnableSwaggerUi(c =>
	        {

## Создание контроллера

5. Щелкните правой кнопкой мыши папку **Контроллеры**, а затем последовательно выберите пункты **Добавить > Контроллер.** 

6. В диалоговом окне **Добавление Scaffold** выберите параметр **Контроллер веб-API 2 — пустой** и нажмите кнопку **Добавить**.

7. Назовите контроллер **ContactNamesController** и щелкните **Добавить**.

## Добавление кода для вызова приложения API

Чтобы вызвать приложение API, защищенное с помощью установки для уровня доступа значения **Внутренний**, необходимо добавить внутренние заголовки аутентификации в HTTP-запросы. Эти заголовки сообщают целевому приложению API, что источник вызова — одноранговое приложение API, вызывающее из той же группы ресурсов.

Пакет SDK службы приложений создает классы клиента, упрощающие код, создаваемый для вызова приложения API. Для вызова приложения API с уровнем доступа **Внутренний (анонимный)** необходимо всего лишь создать объект клиента и методы вызова для него, как описано в следующем примере:

		var client = new ContactsList();
		var contacts = await client.Contacts.GetAsync();

Тем не менее, чтобы добавить заголовки аутентификации, необходимо, чтобы был доступ к объекту `HttpRequestMessage`, которого у вас нет. Чтобы получить доступ к запросу и добавить заголовки, необходимо создать класс `DelegatingHandler` и передать его экземпляр в конструктор созданного клиента.

1. Добавьте в проект файл класса с именем *InternalCredentialHandler.cs* и замените код шаблона следующим кодом.

		using Microsoft.Azure.AppService.ApiApps.Service;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace ContactNames
		{
		    public class InternalCredentialHandler : DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		        {
		            Runtime.FromAppSettings(request).SignHttpRequest(request);
		            return base.SendAsync(request, cancellationToken);
		        }
		    }
		}

	Этот код вызывает `SignHttpRequest`, чтобы добавить заголовки аутентификации в каждый запрос, отправленный классом созданного клиента:

		Runtime.FromAppSettings(this.Request).SignHttpRequest

1. В *ContactNamesController.cs* замените код шаблона следующим кодом.

		using ContactNames.Models;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactNames.Controllers
		{
		    public class ContactNamesController : ApiController
		    {
		        [HttpGet]
		        public async Task<IEnumerable<string>> Get()
		        {
		            var names = new List<string>();

		            var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });
		            var contacts = await client.Contacts.GetAsync();
		
		            foreach (Contact contact in contacts)
		            {
		                names.Add(contact.Name);
		            }		
		            return names;
		        }
		    }
		}

	Этот код передает обработчик конструктору класса созданного клиента:

		var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });

### Развертывание

Вы не можете выполнить тестирование, запустив приложение локально. Необходимо развернуть код и выполнить его в приложении API Azure. В противном случае вы не сможете добавить правильные заголовки аутентификации, а вызовы будут отклоняться.

Более подробное объяснение следующих шагов по развертыванию см. в разделе [Развертывание приложения API](app-service-dotnet-deploy-api-app.md).

1. Создайте приложение API ContactNames.

	* В **Обозреватель решений** щелкните правой кнопкой мыши проект (не решение) и щелкните **Опубликовать**. 

	* Откройте вкладку **Профиль**, а затем щелкните **Приложения API Microsoft Azure**.

	* Нажмите кнопку **Создать**, чтобы подготовить новое приложение API в подписке Azure.

	* В диалоговом окне **Создание приложения API** введите ContactNames в поле **Имя приложения API**.

	* Для других значений в диалоговом окне **Создание приложения API** введите значения, введенные ранее в разделе [Развертывание приложения API](app-service-dotnet-deploy-api-app.md).

		Главное, убедитесь, что вы создаете приложение API в той же группе ресурсов, в которой находится вызываемое приложение API.

	* Нажмите кнопку **ОК**.

2. Разверните код в новом приложении API.

	* После подготовки приложения API щелкните правой кнопкой мыши проект в **Обозреватель решений** и щелкните **Опубликовать**, чтобы повторно открыть диалоговое окно публикации.

	* В диалоговом окне **Публикация в Интернете** щелкните **Опубликовать**, чтобы начать развертывание.

### Тест

В этом разделе пользовательский интерфейс Swagger используется для тестирования нового приложения API и подтверждения того, что оно может вызвать приложение API, созданное ранее.

1. Введите в браузере URL-адрес нового приложения API.

	Если заданы параметры публикации по умолчанию, по завершении процесса публикации Visual Studio переход по URL-адресу приложения API в браузере будет выполнен автоматически. Если этого не произойдет, или вы закрыли это окна браузера, выполните следующие действия, чтобы перейти по тому же URL-адресу:

	* На портале предварительной версии Azure перейдите к колонке приложения API для нового приложения API ContactsName.

	* Щелкните **URL-адрес**.

		![](./media/app-service-api-dotnet-consume-internal/clickurl.png)
  
5. В адресной строке браузера добавьте `/swagger` в конце URL-адреса и нажмите клавишу ВВОД.

	Например, полученный URL-адрес будет выглядеть следующим образом:

		https://microsoft-apiapp214f26e673e5449a214f26e673e5449a.azurewebsites.net/swagger

1. На странице пользовательского интерфейса Swagger щелкните **ContactNames > Получить > Попробовать!**.

	![](./media/app-service-api-dotnet-consume-internal/tryitout.png)
  
	На странице отображаются имена контактов в разделе «Текст ответа», который позволяет проверить, успешно ли приложение ContactNames API получило данные из приложения ContactsList API.

	Если нужно проверить, защищено ли приложение API ContactsList с помощью параметра **Внутренний**, закомментируйте вызов `SignHttpRequest` в файле *ContactNamesController.cs*, разверните приложение еще раз, снова запустите Swagger, щелкните **Попробовать сейчас**, и появится сообщение об ошибке.


## Добавление кода для вызова приложения API с использованием HttpClient
 
Пакет SDK службы приложений зависит от определений API Swagger для создания классов клиента. Если нужно вызвать приложение API, в котором не реализованы определения API Swagger, это можно сделать с помощью `HttpClient`. В этом случае вы по-прежнему можете использовать метод `SignHttpRequest`, но его следует вызывать прямо из объекта `HttpRequestMessage`.

1. В файле *ContactNamesController.cs* добавьте следующий код перед оператором `return names;` в методе `Get`.

		var httpClient = new HttpClient();
		HttpRequestMessage httpRequest = new HttpRequestMessage();
		httpRequest.Method = HttpMethod.Get;
		httpRequest.RequestUri = new Uri("https://{yourapiappurl}/api/contacts");
		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);
		var response = await httpClient.SendAsync(httpRequest); 
		var contacts2 = await response.Content.ReadAsAsync<List<Contact>>();
		foreach (Contact contact in contacts2)
		{
		    names.Add(contact.Name);
		}

	Этот код передает объект входящего запроса в метод `SignHttpRequest`, чтобы подписать объект исходящего запроса:

		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);

2. На портале предварительной версии Azure перейдите к колонке приложения API для приложения API ContactsList и скопируйте URL-адрес.

	![](./media/app-service-api-dotnet-consume-internal/clurl.png)
 
4. Замените строку заполнителя {yourapiappurl} в коде контроллера фактическим URL-адресом. После завершения операции эта строка кода будет выглядеть как в следующем примере.

		httpRequest.RequestUri = new Uri("https://microsoft-apiappd99e684d99e684d99e684d99e684.azurewebsites.net/api/contacts");

### Развертывание и тестирование

1. Выполните ту же процедуру, что и при развертывании кода приложения API ранее.

	**Совет.** Вы можете обойти диалоговое окно **Публикация в Интернете** и развернуть приложение повторно, нажав одну кнопку на панели инструментов. В Visual Studio последовательно щелкните **Представление > Панели инструментов** и включите панель инструментов **Веб-публикация одним щелчком**.
 
2. Выполните ту же процедуру, что и для использования пользовательского интерфейса Swagger ранее.

	Так как вы не добавляли код HttpClient, на этот раз выходные данные представляют собой повторяющийся набор имен.

	![](./media/app-service-api-dotnet-consume-internal/dupnames.png)
  
## Дальнейшие действия

В этой статье показано, как использовать внутреннее приложение API из клиента .NET. Информацию о том, как использовать приложения API, для которых настроен **Общедоступный (аутентифицированный)** и **Общедоступный (анонимный)** уровень доступа, см. в разделе [Использование приложения API в службе приложений Azure из клиента .NET](app-service-api-dotnet-consume.md).

Для получения дополнительных примеров кода, который вызывает приложение API из клиентов .NET, загрузите образец приложения [Карты Azure](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample).

<!---HONumber=58-->
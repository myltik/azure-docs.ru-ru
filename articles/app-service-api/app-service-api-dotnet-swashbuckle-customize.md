
<properties 
	pageTitle="Настройка определений API, создаваемых в Swashbuckle" 
	description="Узнайте, как настроить определения API Swagger, создаваемые с помощью Swashbuckle для приложения API в службе приложений Azure." 
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
	ms.date="08/29/2016" 
	ms.author="rachelap"/>

# Настройка определений API, создаваемых в Swashbuckle 

## Обзор

В этой статье рассматривается настройка Swashbuckle для типичных случаев, когда может требоваться изменение поведения по умолчанию.

* Swashbuckle создает повторяющиеся идентификаторы операций в случае перегрузок методов контроллера.
* Swashbuckle предполагает, что единственным допустимым ответом при вызове метода может быть "HTTP 200 (ОК)".
 
## Настройка параметров создания идентификаторов операций

Swashbuckle создает идентификаторы операций Swagger, объединяя имя контроллера и метода. Использование такого шаблона влечет возникновение проблемы при наличии нескольких перегрузок метода. Swashbuckle создает повторяющиеся идентификаторы операции, которые являются недопустимым файлом Swagger JSON.

Например, следующий код контроллера приводит к тому, что Swashbuckle создает три идентификатора операции Contact\_Get.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Проблему можно решить вручную, присвоив методам уникальные имена как в примере:

* Получить
* GetById;
* GetPage.

Альтернативным вариантом является расширение Swashbuckle, чтобы он автоматически создавал уникальные идентификаторы операций.

На следующих шагах показано, как настроить Swashbuckle, используя файл *SwaggerConfig.cs*, который включен в проект с помощью шаблона проектов предварительной версии приложения API Visual Studio. Кроме того, Swashbuckle можно настроить в проекте веб-API, настроенного для развертывания в качестве приложения API.

1. Создайте пользовательскую реализацию `IOperationFilter`

	Интерфейс `IOperationFilter` предоставляет точку расширения для пользователей Swashbuckle, которым нужно настроить различные параметры обработки метаданных Swagger. В следующем коде показан один из способов изменения поведения при создании идентификатора операции. Код добавляет имена параметров к имени идентификатора операции.

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. В файле *App\_Start\\SwaggerConfig.cs* вызовите метод `OperationFilter`, чтобы библиотека Swashbuckle начала использовать новую реализацию `IOperationFilter`.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	Файл *SwaggerConfig.cs*, размещенный в пакете NuGet Swashbuckle, содержит множество закомментированных примеров точек расширения. Здесь не указаны дополнительные комментарии.

	После внесения этого изменения будет использоваться ваша реализация `IOperationFilter`, в результате чего будут созданы уникальные идентификаторов операции.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>
	
## Коды ответов, отличные от 200

По умолчанию Swashbuckle предполагает, что *единственным* допустимым ответом при вызове метода веб-API может быть "HTTP 200 (ОК)". В некоторых случаях требуется, чтобы в ответе возвращался другой код и при этом клиент не создавал исключений. Например, следующий код веб-API демонстрирует сценарий, в котором клиент будет считать допустимыми ответы с кодами 200 и 404.

	[ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

В этом сценарии код Swagger, создаваемый в Swashbuckle по умолчанию, указывает только один допустимый код состояния HTTP — HTTP 200.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Так как при создании кода клиента программа Visual Studio использует определение API Swagger, полученный код вызывает исключение при любом ответе, отличном от HTTP 200. Ниже приведен код клиента на C#, созданный для нашего метода веб-API.

	if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle позволяет настраивать список ожидаемых кодов в HTTP-ответах двумя способами: с помощью XML-комментариев и атрибута `SwaggerResponse`. Использовать атрибут проще, однако он доступен только в Swashbuckle 5.1.5 и более поздних версиях. Шаблон нового проекта приложения в предварительной версии приложений API в Visual Studio 2013 включает Swashbuckle версии 5.0.0. Если вы использовали такой шаблон, то не сможете обновить Swashbuckle и выполнять настройку нужно будет, используя XML-комментарии.

### Настройка ожидаемых кодов в ответах с помощью XML-комментариев

Используйте этот метод, чтобы задать коды в ответах, если у вас более ранняя версия Swashbuckle, чем 5.1.5.

1. Добавьте XML-комментарии перед методами, для которых нужно указать коды HTML-ответов. Если взять показанное выше действие веб-API и применить к нему XML-документацию, получится код, как в следующем примере.

		/// <summary>
		/// Returns the specified contact.
		/// </summary>
		/// <param name="id">The ID of the contact.</param>
		/// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
		/// <response code="200">OK</response>
		/// <response code="404">Not Found</response>
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();
		
		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

1. Добавьте в файл *SwaggerConfig.cs* инструкции, по которым Swashbuckle определит, что нужно использовать файл XML-документации.

	* Создайте в файле *SwaggerConfig.cs* метод класса *SwaggerConfig*, чтобы указать путь к XML-файлу документации.

			private static string GetXmlCommentsPath()
			{
			    return string.Format(@"{0}\XmlComments.xml", 
			        System.AppDomain.CurrentDomain.BaseDirectory);
			}

	* Прокрутите файл *SwaggerConfig.cs* вниз, пока не увидите закомментированную строку кода (см. снимок экрана).

		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
	
	* Раскомментируйте строку, чтобы включить обработку XML-комментариев при создании кода Swagger.
	
		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
	
1. Чтобы создать файл XML-документации, перейдите в свойства проекта и укажите сведения о файле XML-документации, как показано на следующем снимке экрана.

	![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png)

После выполнения этих шагов в JSON-файле Swagger, созданном Swashbuckle, будут отражаться коды HTTP-ответов, указанные в XML-комментариях. Полезные данные JSON показаны на следующем снимке экрана.

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

При повторном создании кода клиента для интерфейса REST API в Visual Studio код C# принимает HTTP-коды состояния "ОК" и "Не найдено", не вызывая исключений и позволяя получающему коду определять, как обрабатывать возврат пустой записи контакта.

		if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
		{
		    HttpOperationException<object> ex = new HttpOperationException<object>();
		    ex.Request = httpRequest;
		    ex.Response = httpResponse;
		    ex.Body = null;
		    if (shouldTrace)
		    {
		        ServiceClientTracing.Error(invocationId, ex);
		    }
        	    throw ex;
		}

Код для данной демонстрации см. в [этом репозитории GitHub](https://github.com/Azure-Samples/app-service-api-dotnet-swashbuckle-swaggerresponse). Там вы найдете не только размеченный XML-комментариями проект веб-API, но и проект консольного приложения, содержащий клиент, созданный для данного API.

### Настройка ожидаемых кодов ответов с помощью атрибута SwaggerResponse

Атрибут [SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) доступен в Swashbuckle 5.1.5 и более поздних версиях. В случае если в вашем проекте используется более ранняя версия, в этом разделе приведены сведения о том, как обновить пакет NuGet Swashbuckle, чтобы можно было использовать этот атрибут.

1. В **обозревателе решений** щелкните правой кнопкой мыши проект веб-API и выберите элемент **Управление пакетами NuGet**.

	![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. Нажмите кнопку *Обновить* рядом с пакетом NuGet *Swashbuckle*.

	![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. Добавьте атрибуты *SwaggerResponse* к методам действий веб-API, для которых вы хотите указать допустимые коды HTTP-ответов.

		[SwaggerResponse(HttpStatusCode.OK)]
		[SwaggerResponse(HttpStatusCode.NotFound)]
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();

		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

2. Добавьте инструкцию `using` для атрибута пространства имен.

		using Swashbuckle.Swagger.Annotations;
		
1. Откройте папку */swagger/docs/v1* в структуре проекта. URL-адрес проекта и различные коды HTTP-ответов будут отображаться в JSON-файле Swagger.

	![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

Код для данной демонстрации см. в [этом репозитории GitHub](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes). Там вы найдете не только проект веб-API с атрибутом *SwaggerResponse*, но и проект консольного приложения, содержащий клиент, созданный для данного API.

## Дальнейшие действия

В этой статье показано, как настроить создаваемые Swashbuckle идентификаторы операций и допустимые коды ответов. Дополнительную информацию см. в статье [Swashbuckle на GitHub](https://github.com/domaindrivendev/Swashbuckle).
 

<!---HONumber=AcomDC_0831_2016-->
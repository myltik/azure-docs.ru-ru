<properties 
	pageTitle="Развертывание приложения API в службе приложений Azure" 
	description="Узнайте, как развернуть проект приложения API в подписке Azure." 
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
	ms.date="08/14/2015" 
	ms.author="tdykstra"/>

# Развертывание приложения API в службе приложений Azure 

## Обзор

В этом учебнике предстоит развернуть проект веб-API, созданный в [предыдущем учебнике](app-service-dotnet-create-api-app.md), в новое [приложение API](app-service-api-apps-why-best-platform.md). Вы будете использовать Visual Studio для создания ресурса приложения API в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) и развертывания кода веб-API в приложение API Azure.

### Другие варианты развертывания

Существует много других способов развертывания приложений API. Приложение API — это [веб-приложение](../app-service-web/app-service-web-overview.md) с дополнительными возможностями для размещения веб-служб, и все [способы развертывания, которые доступны для веб-приложений](../app-service-web/web-sites-deploy.md), также можно использовать с приложениями API. Веб-приложение, размещающее приложение API, называется хостом приложения API на портале предварительной версии Azure, и вы можете настраивать развертывание с помощью колонки портала хоста приложения API. Сведения о колонке хоста приложения API см. в разделе [Управление приложением API](app-service-api-manage-in-portal.md).

Тот факт, что приложения API основаны на веб-приложениях, также означает, что вы можете развертывать в приложениях API код, написанный для платформ, отличных от ASP.NET. Пример, в котором используется Git для развертывания кода Node.js в приложении API, см. в разделе [Создание приложения API Node.js в службе приложений Azure](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Создание приложения API в Azure 

В этом разделе для создания приложения API в Azure используется мастер **веб-публикации** Visual Studio. Когда вам будет предложено ввести имя для приложения API, введите *ContactsList*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>Развертывание кода в новом приложении API Azure

Для развертывания кода в новом приложении API используйте тот же мастер **публикации веб-проекта**.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Вызов приложения API Azure 

Если вы включили пользовательский интерфейс Swagger, согласно инструкциям предыдущего учебника, проверьте, запущено ли приложение API в Azure с помощью этого интерфейса.

1. На [портале предварительной версии Azure](https://portal.azure.com) перейдите к колонке **Приложение API** для развернутого вами нового приложения API.

2. Щелкните URL-адрес этого приложения API.

	![Щелкните URL-адрес.](./media/app-service-dotnet-deploy-api-app/clickurl.png)

	Откроется страница «Приложение API успешно создано».

3. В адресной строке браузера добавьте в конец URL-адреса текст «/swagger».

4. На открывшейся странице Swagger последовательно выберите пункты **Контакты > Получить > Попробуйте сейчас**.

	![Попробуйте сейчас](./media/app-service-dotnet-deploy-api-app/swaggerui.png)

## Просмотр определения API на портале Azure

1. На [портале предварительной версии Azure](https://portal.azure.com) перейдите к колонке **Приложение API** для развернутого вами нового приложения API.

4. Щелкните **Определение API**.
 
	В колонке **Определение API** приложения отобразится список операций API, определенных при создании приложения.

	![Определение интерфейса API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

Выполнив описанные ниже действия, вы внесете изменение в определение API и посмотрите, отобразилось ли оно на портале.

5. Вернитесь к проекту в Visual Studio и добавьте следующий код в файл **ContactsController.cs**.   

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	Этот код добавляет метод **Post**, который можно использовать для отправки новых экземпляров `Contact` в API.

	Код для класса «Контакты» теперь выглядит, как в приведенном ниже примере.

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
		
		    [HttpPost]
		    public HttpResponseMessage Post([FromBody] Contact contact)
		    {
		        // todo: save the contact somewhere
		        return Request.CreateResponse(HttpStatusCode.Created);
		    }
		}

7. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите команду **Опубликовать**.

9. Перейдите на вкладку **Предварительный просмотр**.

10. Нажмите кнопку **Начать предварительный просмотр**, чтобы увидеть, какие файлы будут скопированы в Azure.

	![Диалоговое окно «Публикация веб-проекта»](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. Щелкните **Опубликовать**.

6. Перезапустите шлюз, как при первой публикации.

12. После завершения процесса публикации вернитесь на портал, закройте и снова откройте колонку **Определение API**. Вы увидите новую конечную точку API, которую только что создали и развернули напрямую в подписке Azure.

	![Определение интерфейса API](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Дальнейшие действия

Вы увидели, как возможности прямого развертывания в Visual Studio упрощают итерацию и ускоряют развертывание и проверку правильности работы API. В [следующем учебнике](../app-service-dotnet-remotely-debug-api-app.md) будет показано, как отлаживать приложение API во время его выполнения в Azure.
 

<!---HONumber=Oct15_HO2-->
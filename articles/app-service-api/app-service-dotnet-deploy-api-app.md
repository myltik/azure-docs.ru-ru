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
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Развертывание приложения API в службе приложений Azure 

## Обзор

В этом учебнике предстоит развернуть проект веб-API, созданный в [предыдущем учебнике](app-service-dotnet-create-api-app.md), в новое [приложение API](app-service-api-apps-why-best-platform.md). Вы будете использовать Visual Studio для создания ресурса приложения API в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) и развертывания кода веб-API в приложение API Azure.

### Другие варианты развертывания

Существует много других способов развертывания приложений API. Приложение API — это [веб-приложение](../app-service-web/app-service-web-overview.md) с дополнительными возможностями для размещения веб-служб, и все [способы развертывания, которые доступны для веб-приложений](../app-service-web/web-sites-deploy.md), также можно использовать с приложениями API. Веб-приложение, размещающее приложение API, называется хостом приложения API на портале предварительной версии Azure, и вы можете настраивать развертывание с помощью колонки портала хоста приложения API. Сведения о колонке хоста приложения API см. в разделе [Управление приложением API](app-service-api-manage-in-portal.md).

Тот факт, что приложения API основаны на веб-приложениях, также означает, что вы можете развертывать в приложениях API код, написанный для платформ, отличных от ASP.NET. Пример, в котором используется Git для развертывания кода Node.js в приложении API, см. в разделе [Создание приложения API Node.js в службе приложений Azure](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Создание приложения API в Azure 

В этом разделе для создания приложения API в Azure используется мастер **веб-публикации** Visual Studio. Когда вам будет предложено ввести имя для приложения API, введите *ContactsList*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>Развертывание кода в новом приложении API

Для развертывания кода в новом приложении API используйте тот же мастер **публикации веб-проекта**.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Просмотр приложения на портале предварительной версии Azure

В этом разделе вы просмотрите основные параметры, доступные для приложений API, и внесете в приложение API итеративные изменения. С каждым развертыванием на портале отражаются изменения, вносимые в приложение API.

1. На [портале предварительной версии Azure](https://portal.azure.com) перейдите к колонке **Приложение API** для развернутого вами нового приложения API.

4. Щелкните **Определение API**.
 
	В колонке **Определение API** приложения отобразится список операций API, определенных при создании приложения.

	![Определение интерфейса API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. Теперь вернитесь в проект в Visual Studio и добавьте следующий код в файл **ContactsController.cs**.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Добавление метода Post в контроллер](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

	Этот код добавляет метод **Post**, который можно использовать для отправки новых экземпляров `Contact` в API.

6. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите команду **Опубликовать**.

	![Контекстное меню публикации проекта](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. Перейдите на вкладку **Параметры**.

8. В раскрывающемся списке **Конфигурация** выберите элемент **Отладка**.

	![Параметры публикации веб-проекта](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. Перейдите на вкладку **Предварительный просмотр**.

10. Нажмите **Начало предварительного просмотра**, чтобы просматривать изменения, которые будут внесены.

	![Диалоговое окно «Публикация веб-проекта»](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. Щелкните **Опубликовать**.

12. После завершения процесса публикации вернитесь на портал, закройте и снова откройте колонку **Определение API**. Вы увидите новую конечную точку API, которую только что создали и развернули напрямую в подписке Azure.

	![Определение интерфейса API](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Дальнейшие действия

Вы увидели, как возможности прямого развертывания в Visual Studio упрощают итерацию и ускоряют развертывание и проверку правильности работы API. В [следующем учебнике](../app-service-dotnet-remotely-debug-api-app.md) будет показано, как отлаживать приложение API во время его выполнения в Azure.
 

<!---HONumber=July15_HO4-->
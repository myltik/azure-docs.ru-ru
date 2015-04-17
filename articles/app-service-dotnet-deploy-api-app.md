<properties 
	pageTitle="Развертывание приложения API службы приложений Azure" 
	description="Развертывание проекта приложения API в подписке Azure." 
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

# Развертывание приложения API службы приложений Azure

## Обзор

Если вы разрабатываете собственное приложение API, используя Visual Studio, и вам нужно протестировать API в облаке, можно создать новое приложение API в подписке Azure и развернуть код с помощью удобных функций развертывания службы приложений Visual Studio. 

Это второй из трех учебников серии:

1. в учебнике [Создание приложения API](app-service-dotnet-create-api-app.md) вы создали проект приложения API. 
* В этом учебнике вы развернете приложение API в подписке Azure.
* В учебнике [Отладка приложения API](app-service-dotnet-remotely-debug-api-app.md) вы будете использовать Visual Studio для удаленной отладки кода во время его выполнения в Azure.

## Развертывание приложения API 

В **обозревателе решений** щелкните правой кнопкой мыши проект (не решение) и выберите **Опубликовать...**. 

![Project publish menu option](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

Откройте вкладку **Профиль** и щелкните **Приложения API Microsoft Azure (предварительная версия)**. 

![Publish Web dialog](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

Щелкните **Создать**, чтобы подготовить новое приложение API в подписке Azure.

![Select Existing API Services dialog](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

В диалоговом окне **Создание приложения API** введите следующее:

- В поле **Имя приложения API** введите имя приложения. 
- Если у вас несколько подписок Azure, выберите ту, которую хотите использовать.
-В меню "План службы приложений" выберите один из существующих планов службы приложений или щелкните **Создать план службы приложений** и введите имя нового плана. 
- В меню **Группа ресурсов** выберите одну из существующих групп ресурсов или щелкните **Создать новую группу ресурсов** и введите имя. Имя должно быть уникальным; рассмотрите возможность использования имени приложения в качестве префикса и добавления персональных данных, например учетной записи Майкрософт (без символа "@").  
- В меню **Уровень доступа** выберите **Доступно всем пользователям**. Этот параметр сделает API общедоступным, что подходит для этого учебника. Вы можете ограничить доступ позже, на портале Azure.
- Выберите регион.  

![Configure Microsoft Azure Web App dialog](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

Нажмите кнопку **ОК**, чтобы создать приложение API в подписке. Процесс может занять несколько минут, поэтому в Visual Studio отобразится окно с уведомлением об инициации процесса. 

![API Service Creation Started confirmation message](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

Процесс подготовки создаст группу ресурсов и приложение API в подписке Azure. Ход выполнения отображается в окне **Действия службы приложений Azure** Visual Studio. 

![Status notification via the Azure App Service Activity window](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

После подготовки приложения API щелкните правой кнопкой мыши проект в обозревателе решений и нажмите кнопку **Опубликовать**, чтобы повторно открыть диалоговое окно публикации. Профиль публикации, созданный на предыдущем шаге, должен быть уже выбран. Нажмите кнопку **Опубликовать**, чтобы начать процесс развертывания. 

![Deploying the API App](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

В окне **Действия службы приложений Azure** отображается ход развертывания. 

![Status notification of the Azure App Service Activity window](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

## Просмотр приложения на портале Azure

В этом разделе вы перейдете на портал для просмотра основных параметров, доступных для приложений API, и внесете изменения в итерацию приложения API. После каждого развертывания на портале будут отображаться изменения, внесенные в приложение API. 

В браузере перейдите на [портал Azure](https://portal.azure.com). 

Нажмите кнопку **Обзор** на боковой панели и щелкните **Приложения API**.

![Browse options on Azure portal](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

Выберите созданный API из списка приложений API в подписке.

![API Apps list](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

Щелкните **Определение API**. В колонке **Определение API** приложения отобразится список операций API, определенных при создании приложения. 

![API Definition](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

Теперь вернитесь к проекту в Visual Studio. Добавьте следующий код в файл **ContactsController.cs**.  

	[HttpPost]
	public HttpResponseMessage Post([FromBody] Contact contact)
	{
		// todo: save the contact somewhere
		return Request.CreateResponse(HttpStatusCode.Created);
	}

Этот код добавляет метод **POST**, который можно использовать для публикации новых экземпляров `Contact` в API. 

![Adding the Post method to the controller](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Опубликовать**. 

![Project Publish context menu](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

Выберите конфигурацию **Отладка** в раскрывающемся списке **Конфигурация** и щелкните **Опубликовать**, чтобы повторно развернуть приложение API. 

![Publish Web settings](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

На вкладке **Предварительный просмотр** мастера **публикации в Интернете** щелкните **Опубликовать**.  

![Publish Web dialog](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

После завершения процесса публикации вернитесь на портал, закройте и снова откройте колонку **Определение API**. Вы увидите новую конечную точку API, которую только что создали и развернули напрямую в подписке Azure.

![API Definition](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Дальнейшие действия

Вы увидели, как возможности прямого развертывания в Visual Studio упрощают итерацию и ускоряют развертывание и проверку правильности работы API. В [следующем учебнике](app-service-dotnet-remotely-debug-api-app.md) вы узнаете, как отладить приложение API во время его выполнения в Azure.

<!--HONumber=49-->
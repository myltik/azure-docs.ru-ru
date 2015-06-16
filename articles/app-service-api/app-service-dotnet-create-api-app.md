<properties 
	pageTitle="Создание приложения API ASP.NET в службе приложений Azure" 
	description="Узнайте, как создать приложение API в службе приложений Azure с помощью Visual Studio 2013." 
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

# Создание приложения API ASP.NET в службе приложений Azure

## Обзор

В этом учебнике показывается, как создать проект веб-API ASP.NET с нуля и настроить его для развертывания в виде [приложения API](app-service-api-apps-why-best-platform.md) в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md). Если у вас имеется существующий проект веб-API, который требуется преобразовать в приложение API, см. статью [Настройка проекта веб-API как приложения API](./app-service-dotnet-create-api-app-visual-studio). В последующих учебниках из этой серии показывается, как [развертывать](app-service-dotnet-deploy-api-app.md) и [отлаживать](../app-service-dotnet-remotely-debug-api-app.md) проект приложения API, который будет создаваться в этом учебнике.

Сведения о приложениях API см. в разделе [Что такое приложения API?](app-service-api-apps-why-best-platform.md).

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Для этого учебника требуется пакет SDK Azure для .NET версии 2.5.1 или более поздней.

## Создание проекта приложения API 

В этом разделе показывается, как использовать шаблон проекта приложения API Azure для создания приложения API с нуля. Сведения о настройке существующего проекта веб-API как приложения API см. в [следующем разделе](#configure-a-web-api-project-as-an-api-app).

1. Откройте Visual Studio 2013.

2. Выберите **Файл -> Новый проект**.

3. Выберите шаблон **Веб-приложение ASP.NET**.

4. Дайте проекту имя *ContactsList*.

	![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

5. Нажмите кнопку **ОК**.

6. В диалоговом окне **Новый проект ASP.NET** выберите шаблон **Приложение API Azure**.

	![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

7. Нажмите кнопку **ОК**, чтобы создать проект.

Visual Studio создает проект веб-API, настроенный для развертывания в виде приложения API.

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Дальнейшие действия

Теперь ваше приложение API готово к развертыванию и вы можете перейти к учебнику [Развертывание приложения API](app-service-dotnet-deploy-api-app.md).

<!--HONumber=54--> 
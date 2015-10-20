<properties
	pageTitle="Создание приложения API ASP.NET в службе приложений Azure | Microsoft Azure"
	description="Узнайте, как создать приложение API ASP.NET в службе приложений Azure с помощью Visual Studio 2013."
	services="app-service\api"
	documentationCenter=".net"
	authors="bradygaster"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/08/2015"
	ms.author="tdykstra"/>

# Создание приложения API ASP.NET в службе приложений Azure

> [AZURE.SELECTOR]
- [.NET - Visual Studio 2015](app-service-dotnet-create-api-app.md)
- [.NET - Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)
- [Node.js](app-service-api-nodejs-api-app.md)
- [Java](app-service-api-java-api-app.md)

## Обзор

В этом учебнике показывается, как создать проект веб-API ASP.NET и настроить его для развертывания в облаке в виде [приложения API в службе приложений Azure](app-service-api-apps-why-best-platform.md). Информацию о настройке существующего проекта веб-API для развертывания в качестве приложения API см. в статье [Настройка проекта веб-API как приложения API](app-service-dotnet-create-api-app-visual-studio.md).

Этот быстрый и простой учебник объясняет, как создать проект Visual Studio с помощью шаблона. Этот учебник является первым в серии, которая также поясняет [развертывание](app-service-dotnet-deploy-api-app.md) и [отладку](../app-service-dotnet-remotely-debug-api-app.md) проекта приложения API, который будет создаваться в этом учебнике. Дополнительные сведения о работе с приложениями API см. в разделе [Дальнейшие действия](#next-steps) в конце учебника.

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Для этого учебника требуется пакет SDK Azure для .NET версии 2.6 или более поздней.

## Создание проекта приложения API

Когда вам будет предложено ввести имя проекта, введите **ContactsList**.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Дальнейшие действия

Теперь ваше приложение API готово к развертыванию, и вы можете перейти к учебнику [Развертывание приложения API](app-service-dotnet-deploy-api-app.md).

Сведения об использовании автоматически создаваемого клиентского кода для вызова приложений API см. в статье [Использование приложения API из клиента .NET](app-service-api-dotnet-consume.md).

Сведения о настройке автоматически созданных метаданных Swagger для приложения API см. в статье [Настройка определений API, созданных Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md).

Сведения о способах создания, удаления и настройки приложений API на портале предварительной версии Azure см. в статье [Управление приложениями API](app-service-api-manage-in-portal.md).

Сведения о проверке подлинности пользователей приложений API см. в статье [Проверка подлинности для приложений API и мобильных приложений в службе приложений Azure](../app-service/app-service-authentication-overview.md).

Сведения о функциях приложений API см. в разделе [Что такое приложения API?](app-service-api-apps-why-best-platform.md).

<!---HONumber=Oct15_HO3-->
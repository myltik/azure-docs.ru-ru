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
	ms.topic="get-started-article" 
	ms.date="05/19/2015"
	ms.author="bradyg;tarcher"/>

# Создание приложения API ASP.NET в службе приложений Azure

> [AZURE.SELECTOR]
- [Visual Studio 2013](app-service-dotnet-create-api-app.md)
- [Visual Studio 2015 RC](app-service-dotnet-create-api-app-vs2015.md)
- [Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)

## Обзор

В этом учебнике показано, как создать проект веб-интерфейса API ASP.NET с помощью шаблона Visual Studio 2013, который позволяет настроить проект для развертывания в облаке в качестве [приложения API](app-service-api-apps-why-best-platform.md) в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md). Информацию о настройке существующего проекта веб-API для развертывания в качестве приложения API см. в статье [Настройка проекта веб-API как приложения API](app-service-dotnet-create-api-app-visual-studio.md).

В последующих учебниках из этой серии показывается, как [развертывать](app-service-dotnet-deploy-api-app.md) и [отлаживать](../app-service-dotnet-remotely-debug-api-app.md) проект приложения API, который будет создаваться в этом учебнике.

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Для этого учебника требуется пакет SDK Azure для .NET версии 2.5.1 или более поздней.

## Создание проекта приложения API

Когда вам будет предложено ввести имя для проекта, введите *ContactsList*.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

[AZURE.INCLUDE [app-service-api-review-metadata](../../includes/app-service-api-review-metadata.md)]

[AZURE.INCLUDE [app-service-api-define-api-app](../../includes/app-service-api-define-api-app.md)]

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Дальнейшие действия

Теперь ваше приложение API готово к развертыванию, и вы можете перейти к учебнику [Развертывание приложения API](app-service-dotnet-deploy-api-app.md).
 

<!---HONumber=July15_HO3-->
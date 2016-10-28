<properties
	pageTitle="Добавление проверки подлинности на iOS с помощью мобильных приложений Azure"
	description="Узнайте, как использовать мобильные приложения для проверки подлинности пользователей приложения iOS с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/28/2016"
	ms.author="krisragh"/>

# Добавление проверки подлинности в приложение iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

В этом учебнике описывается добавление проверки подлинности в проект учебника по [быстрому запуску iOS] с помощью поддерживаемого поставщика удостоверений. Этот учебник использует материал учебника по [быстрому запуску iOS], который необходимо пройти в первую очередь.

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

В Xcode нажмите кнопку **Выполнить**, чтобы запустить приложение. Будет создано исключение, так как приложение попытается получить доступ к серверной части как пользователь, не прошедший проверку подлинности, а для таблицы _TodoItem_ теперь требуется проверка подлинности.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[быстрому запуску iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com

<!---HONumber=AcomDC_0706_2016-->
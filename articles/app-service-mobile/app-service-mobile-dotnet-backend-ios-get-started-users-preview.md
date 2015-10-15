<properties
	pageTitle="Добавление проверки подлинности на iOS с помощью мобильных приложений Azure"
	description="Узнайте, как использовать мобильные приложения для проверки подлинности пользователей приложения iOS с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="krisragh"/>

# Проверка подлинности iOS с помощью мобильных приложений Azure

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

В этом учебнике описывается добавление проверки подлинности в проект, составленный при изучении учебника по [быстрому запуску iOS], с помощью поддерживаемого поставщика удостоверений. Этот учебник составлен на основе учебника по [быстрому запуску iOS], который необходимо пройти в первую очередь. Если вы не используете скачанный проект быстрого запуска сервера, в проект необходимо добавить пакет расширений для аутентификации. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="create-gateway"></a>Создание шлюза службы приложений

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

В Xcode нажмите кнопку **Выполнить**, чтобы запустить приложение. Будет создано исключение, так как приложение попытается получить доступ к серверной части как пользователь, не прошедший аутентификацию, а таблица _TodoItem_ теперь требует аутентификации.

##<a name="add-authentication"></a>Добавление проверки подлинности в приложение

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[быстрому запуску iOS]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!----HONumber=August15_HO9-->
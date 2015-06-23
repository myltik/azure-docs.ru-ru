<properties
	pageTitle="Вызов настраиваемого интерфейса API из клиента iOS"
	description="Узнайте, как определить настраиваемый API и вызывать его из приложения iOS, которое использует мобильные службы Azure."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	writer="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/03/2015"
	ms.author="krisragh"/>


# Вызов настраиваемого интерфейса API из клиента iOS (серверная часть .NET)

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

В этом разделе показано, как вызывать настраиваемый интерфейс API из приложений iOS. Настраиваемый интерфейс API позволяет определить пользовательские конечные точки с функциональными возможностями сервера, но он не сопоставляет их с операциями вставки, обновления, удаления или чтения базы данных. Настраиваемый интерфейс API предоставляет больше возможностей для контроля над сообщениями, включая заголовки HTTP и формат текста сообщения.

## <a name="define-custom-api"></a>Определение настраиваемого интерфейса API

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](mobile-services-ios-call-custom-api.md)]

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Mobile Services Quick Start]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Store server scripts in source control]: mobile-services-store-scripts-source-control.md

<!--HONumber=54--> 
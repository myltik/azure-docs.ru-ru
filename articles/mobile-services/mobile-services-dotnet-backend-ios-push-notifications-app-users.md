<properties
	pageTitle="Рассылка push-уведомлений проверенным пользователям (серверная часть .NET)"
	description="Узнайте, как отправлять push-уведомления для конкретного адресата"
	services="mobile-services,notification-hubs"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Рассылка push-уведомлений проверенным пользователям

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

В этом разделе показано, как отправлять push-уведомления пользователю, прошедшему проверку подлинности, на iOS-устройстве. Перед этим учебником сперва выполните задания учебников [Приступая к работе с проверкой подлинности] и [Приступая к работе с push-уведомления].

В этом учебнике вы узнаете, как требовать от пользователей проверку подлинности, регистрироваться в концентраторе уведомлений для рассылки push-уведомлений и обновлять серверные скрипты для отправки этих уведомлений только пользователям, прошедшим проверку подлинности.

##<a name="register"></a>Обновление службы, чтобы для регистрации требовалась проверка подлинности

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Обновление приложения для выполнения входа перед регистрацией

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Тестирование приложения

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Приступая к работе с проверкой подлинности]: mobile-services-dotnet-backend-ios-get-started-users.md
[Приступая к работе с push-уведомления]: mobile-services-dotnet-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=AcomDC_1203_2015-->
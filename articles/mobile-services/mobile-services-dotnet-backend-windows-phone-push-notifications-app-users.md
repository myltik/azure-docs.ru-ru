<properties
	pageTitle="Рассылка push-уведомлений проверенным пользователям"
	description="Узнайте, как отправлять push-уведомления для конкретного адресата"
	services="mobile-services,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="glenga"/>

# Рассылка push-уведомлений проверенным пользователям

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

В этом разделе демонстрируется отправка push-уведомлений прошедшему проверку подлинности пользователю на любом зарегистрированном устройстве. В отличие от предыдущего учебника [Добавление push-уведомлений в приложение мобильных служб], в данном учебнике будут описаны изменения мобильной службы, обеспечивающие выполнение проверки подлинности пользователя перед регистрацией клиента в концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется — добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется серверный код, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.

Этот учебник поддерживает приложения Silverlight Windows Phone 8.0 и Windows Phone 8.1. Для приложений Магазина Windows Phone 8.1 см. [версию этой статьи для Магазина Windows](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).

##Предварительные требования

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Добавление проверки подлинности в приложение]<br/>В демонстрационное приложение TodoList добавляется требование выполнить вход.

+ [Добавление push-уведомлений к приложению мобильных служб] <br/>Выполняется настройка демонстрационного приложения TodoList для push-уведомлений с использованием центров уведомлений.

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

##<a name="register"></a>Обновление службы, чтобы для регистрации требовалась проверка подлинности

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Обновление приложения, чтобы для регистрации требовался вход

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../../includes/mobile-services-windows-phone-push-notifications-app-users.md)]


##<a name="test"></a>Тестирование приложения

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Добавление проверки подлинности в приложение]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Добавление push-уведомлений в приложение мобильных служб]: mobile-services-dotnet-backend-windows-phone-get-started-push.md
[Добавление push-уведомлений к приложению мобильных служб]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/

<!---HONumber=September15_HO1-->
<properties
	pageTitle="Рассылка push-уведомлений проверенным пользователям"
	description="Узнайте, как отправлять push-уведомления для конкретного адресата"
	services="mobile-services,notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="notification-hubs"
	ms.devlang="java"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="wesmc"/>

# Рассылка push-уведомлений проверенным пользователям

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Обзор

В этом разделе демонстрируется отправка push-уведомлений прошедшему проверку подлинности пользователю на любом зарегистрированном устройстве. В отличие от предыдущего учебника по [push-уведомлениям][Get started with push notifications], в данном учебнике будет изменяться мобильная служба, чтобы она требовала выполнение проверки подлинности пользователя перед регистрацией клиента в концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется — добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется серверный код, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.


Этот учебник поддерживает приложения Android.

##Предварительные требования

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Добавление проверки подлинности в приложение мобильных служб]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Приступая к работе с push-уведомлениями в мобильных службах]<br/>В этом учебнике выполняется настройка демонстрационного приложения TodoList для push-уведомлений с использованием концентратора уведомлений.

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

##Обновление службы с учетом требования по проверке подлинности для регистрации

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##Обновление приложения с учетом требования по входу в систему перед регистрацией

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](../../includes/mobile-services-android-push-notifications-app-users.md)]

##Тестирование приложения

[AZURE.INCLUDE [mobile-services-android-test-push-users](../../includes/mobile-services-android-test-push-users.md)]


<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users](mobile-services-javascript-backend-service-side-authorization.md), you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Добавление проверки подлинности в приложение мобильных служб]: mobile-services-dotnet-backend-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md
[Приступая к работе с push-уведомлениями в мобильных службах]: mobile-services-dotnet-backend-android-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=September15_HO1-->
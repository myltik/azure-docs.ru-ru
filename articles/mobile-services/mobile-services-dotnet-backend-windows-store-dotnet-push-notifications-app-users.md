<properties 
	pageTitle="Рассылка push-уведомлений пользователям, прошедшим проверку подлинности (универсальное приложение для Windows 8.1) | Мобильные службы Azure" 
	description="Узнайте, как использовать мобильные службы Azure для отправки push-уведомлений конкретному прошедшему проверку подлинности пользователю при помощи универсального приложения для Windows 8.1." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="glenga"/>

# Рассылка push-уведомлений проверенным пользователям

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Обзор

В этом разделе демонстрируется отправка push-уведомлений прошедшему проверку подлинности пользователю на любом зарегистрированном устройстве. В отличие от предыдущего учебника по [push-уведомлениям][Get started with push notifications], в данном учебнике будет изменяться мобильная служба, чтобы она требовала выполнение проверки подлинности пользователя перед регистрацией клиента в концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется — добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется серверный код, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.
 
Этот учебник поддерживает приложения Магазина Windows и приложения Магазина Windows Phone.

##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Приступая к работе с аутентификацией в мобильных службах] В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Приступая к работе с push-уведомлениями в мобильных службах] В этом учебнике выполняется настройка демонстрационного приложения TodoList для push-уведомлений с использованием центров уведомлений.

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

##<a name="register"></a>Обновление службы, чтобы для регистрации требовалась проверка подлинности

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Обновление приложения, чтобы для регистрации требовался вход

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>Тестирование приложения

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Приступая к работе с аутентификацией в мобильных службах]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Приступая к работе с push-уведомлениями в мобильных службах]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/

 

<!---HONumber=Oct15_HO2-->
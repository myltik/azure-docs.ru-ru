<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="krisragh" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Рассылка push-уведомлений проверенным пользователям

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users" title="Магазин Windows JavaScript" >Магазин Windows JavaScript</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users" title="iOS" class="current">iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/" title="Серверная часть .NET" class="current">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/"  title="Серверная часть JavaScript">Серверная часть JavaScript</a></div>

В этом разделе демонстрируется отправка push-уведомлений прошедшему проверку подлинности пользователю на любом зарегистрированном устройстве iOS. В отличие от предыдущего учебника по [push-уведомлениям][push-уведомлениям], в данном учебнике будет изменяться мобильная служба, чтобы она требовала выполнение проверки подлинности пользователя перед регистрацией клиента в концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется — добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется серверный код, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.

В этом учебнике выполняются следующие действия.

-   [Обновление службы, чтобы для регистрации требовалась проверка подлинности][Обновление службы, чтобы для регистрации требовалась проверка подлинности]
-   [Обновление приложения для выполнения входа перед регистрацией][Обновление приложения для выполнения входа перед регистрацией]
-   [Тестирование приложения][Тестирование приложения]

Этот учебник поддерживает приложения Магазина Windows и приложения Магазина Windows Phone.

## Предварительные требования

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

-   [Приступая к работе с аутентификацией в мобильных службах][Приступая к работе с аутентификацией в мобильных службах]
    В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

-   [Приступая к работе с push-уведомлениями в мобильных службах][push-уведомлениям]
    В этом учебнике выполняется настройка демонстрационного приложения TodoList для push-уведомлений с использованием концентратора уведомлений.

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

## <a name="register"></a>Обновление службы, чтобы для регистрации требовалась проверка подлинности

[WACOM.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users][mobile-services-dotnet-backend-push-notifications-app-users]]

## <a name="update-app"></a>Обновление приложения для выполнения входа перед регистрацией

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-login][mobile-services-ios-push-notifications-app-users-login]]

## <a name="test"></a> Тестирование приложения

[WACOM.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app][mobile-services-ios-push-notifications-app-users-test-app]]

<!-- Anchors. --> 
<!-- URLs. -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users "Магазин Windows C#"
  [Магазин Windows JavaScript]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users "Магазин Windows JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users "Android"
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/ "Серверная часть .NET"
  [Серверная часть JavaScript]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/ "Серверная часть JavaScript"
  [push-уведомлениям]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
  [Обновление службы, чтобы для регистрации требовалась проверка подлинности]: #register
  [Обновление приложения для выполнения входа перед регистрацией]: #update-app
  [Тестирование приложения]: #test
  [Приступая к работе с аутентификацией в мобильных службах]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
  [mobile-services-dotnet-backend-push-notifications-app-users]: ../includes/mobile-services-dotnet-backend-push-notifications-app-users.md
  [mobile-services-ios-push-notifications-app-users-login]: ../includes/mobile-services-ios-push-notifications-app-users-login.md
  [mobile-services-ios-push-notifications-app-users-test-app]: ../includes/mobile-services-ios-push-notifications-app-users-test-app.md

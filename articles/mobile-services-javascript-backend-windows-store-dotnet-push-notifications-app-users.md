<properties linkid="/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Рассылка push-уведомлений проверенным пользователям

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="Магазин Windows C#" class="current">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android">Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе демонстрируется отправка push-уведомлений прошедшему проверку подлинности пользователю на любом зарегистрированном устройстве. В отличие от предыдущего учебника по [push-уведомлениям][push-уведомлениям], в данном учебнике будет изменяться мобильная служба, чтобы она требовала выполнение проверки подлинности пользователя перед регистрацией клиента в концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется — добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется скрипт сервера, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.

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

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users][mobile-services-javascript-backend-push-notifications-app-users]]

<ol start="5"><li><p>Замените функцию вставки следующим кодом и нажмите кнопку <b>Сохранить</b>.</p>

<pre><code>function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '&lt;?xml version="1.0" encoding="utf-8"?&gt;&lt;toast&gt;&lt;visual&gt;' +    
    '&lt;binding template="ToastText01"&gt;&lt;text id="1"&gt;' +
    item.text + '&lt;/text&gt;&lt;/binding&gt;&lt;/visual&gt;&lt;/toast&gt;';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
	    	// registered to the logged-in user as a tag.
            	push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
}</code></pre>

    <p>Этот скрипт вставки использует тег идентификатора пользователя для отправки push-уведомления (с текстом вставленного элемента) во все регистрации приложения Магазина Windows, созданные выполнившим вход пользователем.</p></li></ol>

## <a name="update-app"></a>Обновление приложения для выполнения входа перед регистрацией

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users][mobile-services-windows-store-dotnet-push-notifications-app-users]]

## <a name="test"></a> Тестирование приложения

[WACOM.INCLUDE [mobile-services-windows-test-push-users][mobile-services-windows-test-push-users]]

<!---## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]--> 

<!-- Anchors. --> 
<!-- URLs. -->

  [Магазин Windows C#]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users "Магазин Windows C#"
  [Магазин Windows JavaScript]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users "Магазин Windows JavaScript"
  [Windows Phone]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users "Windows Phone"
  [iOS]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users "iOS"
  [Android]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users "Android"
  [Серверная часть .NET]: /ru-ru/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/ "Серверная часть .NET"
  [Серверная часть JavaScript]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/ "Серверная часть JavaScript"
  [push-уведомлениям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Обновление службы, чтобы для регистрации требовалась проверка подлинности]: #register
  [Обновление приложения для выполнения входа перед регистрацией]: #update-app
  [Тестирование приложения]: #test
  [Приступая к работе с аутентификацией в мобильных службах]: /ru-ru/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [mobile-services-javascript-backend-push-notifications-app-users]: ../includes/mobile-services-javascript-backend-push-notifications-app-users.md
  [mobile-services-windows-store-dotnet-push-notifications-app-users]: ../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md
  [mobile-services-windows-test-push-users]: ../includes/mobile-services-windows-test-push-users.md

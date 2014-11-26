<properties linkid="/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" pageTitle="Send push notifications to authenticated users" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Learn how to send push notifications to specific " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Send push notifications to authenticated users" authors="wesmc" solutions="Mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-android" ms.devlang="java" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc" />

# Рассылка push-уведомлений проверенным пользователям

<div class="dev-center-tutorial-selector sublanding">
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users" title="Магазин Windows C#">Магазин Windows C#</a>
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users" title="Windows Phone">Windows Phone</a>
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users" title="iOS">iOS</a>
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
<a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-push-notifications-app-users/" title="Сервер .NET">Сервер .NET</a> | 
<a href="/ru-ru/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/"  title="Сервер JavaScript" class="current">Сервер JavaScript</a>
</div>

В этом разделе демонстрируется отправка push-уведомлений прошедшему проверку подлинности пользователю на любом зарегистрированном устройстве. В отличие от предыдущего учебника по [push-уведомлениям][push-уведомлениям], в данном учебнике будет изменяться мобильная служба, чтобы она требовала выполнение проверки подлинности пользователя перед регистрацией клиента в концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется — добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется скрипт сервера, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.

В этом учебнике выполняются следующие действия.

-   [Обновление службы, чтобы для регистрации требовалась проверка подлинности][Обновление службы, чтобы для регистрации требовалась проверка подлинности]
-   [Обновление приложения для выполнения входа перед регистрацией][Обновление приложения для выполнения входа перед регистрацией]
-   [Тестирование приложения][Тестирование приложения]

Этот учебник поддерживает приложения Android.

## Предварительные требования

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

-   [Приступая к работе с аутентификацией в мобильных службах][Приступая к работе с аутентификацией в мобильных службах]
    В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

-   [Приступая к работе с push-уведомлениями в мобильных службах][push-уведомлениям]
    В этом учебнике выполняется настройка демонстрационного приложения TodoList для push-уведомлений с использованием концентратора уведомлений.

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

## <a name="register"></a>Обновление службы, чтобы для регистрации требовалась проверка подлинности

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>Замените функцию вставки следующим кодом и нажмите кнопку <b>Сохранить</b>.</p>

<pre><code>function insert(item, user, request) {

    // Define a payload for the Google Cloud Messaging toast notification.
    var payload = 
        '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
            // registered to the logged-in user as a tag.
            push.gcm.send(userId, payload, {
                success: function(pushResponse) {
                    console.log("Sent push with " + userId + " tag:", pushResponse, payload);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                },
                error: function(err) {
                    console.log("request.execute error", err)
                    request.respond();
                }
            });
}</code></pre>

    <p>Этот скрипт вставки использует тег идентификатора пользователя для отправки push-уведомления (с текстом вставленного элемента) во все регистрации Google Cloud Messaging, созданные выполнившим вход пользователем.</p></li></ol>

## <a name="update-app"></a>Обновление приложения для выполнения входа перед регистрацией

[WACOM.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)]

## <a name="test"></a> Тестирование приложения

[WACOM.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)]

<!---## <a name="next-steps"> </a>Next steps  In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]--> 

 


  [push-уведомлениям]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Обновление службы, чтобы для регистрации требовалась проверка подлинности]: #register
  [Обновление приложения для выполнения входа перед регистрацией]: #update-app
  [Тестирование приложения]: #test
  [Приступая к работе с аутентификацией в мобильных службах]: /ru-ru/documentation/articles/mobile-services-android-get-started-users/

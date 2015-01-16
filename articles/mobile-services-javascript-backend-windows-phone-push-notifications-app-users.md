<properties pageTitle="Рассылка push-уведомлений проверенным пользователям" metaKeywords="push notifications, authentication, users, Notification Hubs, Mobile Services" description="Узнайте, как отправлять push-уведомления для конкретного адресата " metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="Mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="glenga" />

# Рассылка push-уведомлений проверенным пользователям

[WACOM.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

В этом разделе демонстрируется отправка push-уведомлений прошедшему проверку подлинности пользователю на любом зарегистрированном устройстве. В отличие от предыдущего учебника [Push-уведомление][Начало работы с push-уведомлениями], в данном учебнике внесены изменения в мобильную службу, чтобы пользователю предъявлялось требование пройти проверку подлинности перед регистрацией клиента на концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется - добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется скрипт сервера, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.

В этом учебнике выполняются следующие действия.

+ [Обновление службы, чтобы для регистрации требовалась проверка подлинности]
+ [Обновление приложения для выполнения входа перед регистрацией]
+ [Тестирование приложения]
 
Этот учебник поддерживает приложения Windows Phone 8.0 и Windows Phone 8.1 ("Silverlight"). Для приложений из Магазина Windows Phone 8.1 см. [версию этой статьи для Магазина Windows](/ru-ru/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users).

##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Начало работы с проверкой подлинности]<br/>Добавление требования по регистрации в пример приложения TodoList.

+ [Начало работы с push-уведомлениями]<br/>Настройка примера приложения TodoList для отправки push-уведомлений с использованием концентраторов уведомлений. 

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

##<a name="register"></a>Обновление службы с учетом требования по проверке подлинности для регистрации

[WACOM.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

<ol start="5"><li><p>Замените функцию вставки следующим кодом и нажмите <strong>Сохранить</strong>:</p>
<pre><code>function insert(item, user, request) {
	// Define a payload for the Windows Phone toast notification.
	var payload = '<?xml version="1.0" encoding="utf-8"?>' +
		'<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
		'<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
		'</wp:Text2></wp:Toast></wp:Notification>';

	// Get the ID of the logged-in user.
	var userId = user.userId;		

	request.execute({
		success: function() {
			// If the insert succeeds, send a notification.
			push.mpns.send(userId, payload, 'toast', 22, {
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

<p>Этот скрипт вставки использует тег идентификатора пользователя для отправки push-уведомления (с текстом вставленного элемента) во все регистрации приложения Windows Phone (MPNS), созданные выполнившим вход пользователем.</p></li></ol>

##<a name="update-app"></a>Обновление приложения с учетом требования по входу в систему перед регистрацией

[WACOM.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../includes/mobile-services-windows-phone-push-notifications-app-users.md)] 


##<a name="test"></a>Тестирование приложения

[WACOM.INCLUDE [mobile-services-windows-test-push-users](../includes/mobile-services-windows-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Дальнейшие действия

В следующем учебнике, [Авторизация пользователей мобильных служб с помощью этих служб][Проверка подлинности пользователей на основе скриптов], показано, как идентификатор пользователя, прошедшего проверку подлинности, который предоставлен мобильными службами, применяется для фильтрации данных, возвращаемых мобильными службами. Сведения об использовании мобильных службах на платформа .NET приведены в [Концептуальном справочнике по мобильным службам на платформе .NET]-->

<!-- Anchors. -->
[Обновление службы, чтобы для регистрации требовалась проверка подлинности]: #register
[Обновление приложения для выполнения входа перед регистрацией]: #update-app
[Тестирование приложения]: #test
[Дальнейшие действия]:#next-steps


<!-- URLs. -->
[Приступая к работе с аутентификацией]: /ru-ru/documentation/articles/mobile-services-windows-phone-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник принципов использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library

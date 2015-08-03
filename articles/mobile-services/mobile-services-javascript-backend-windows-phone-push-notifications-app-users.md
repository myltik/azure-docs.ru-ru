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
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Рассылка push-уведомлений проверенным пользователям

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

В этом разделе демонстрируется отправка push-уведомлений прошедшему проверку подлинности пользователю на любом зарегистрированном устройстве. В отличие от предыдущего учебника [Добавление push-уведомлений в приложение мобильных служб], в данном учебнике будут описаны изменения мобильной службы, обеспечивающие выполнение проверки подлинности пользователя перед регистрацией клиента в концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется — добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется серверный скрипт, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.
 
>[AZURE.NOTE]Этот учебник поддерживает приложения Silverlight Windows Phone 8.0 и Windows Phone 8.1. Для приложений Магазина Windows Phone 8.1 см. [версию этой статьи для Магазина Windows](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md).

##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Добавление проверки подлинности в приложение]<br/>Добавляет требование входа в демонстрационное приложение TodoList.

+ [Добавление push-уведомлений в приложение]<br/>Настраивает демонстрационное приложение TodoList для push-уведомлений с использованием центров уведомлений.

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

##<a name="register"></a>Обновление службы с учетом требования по проверке подлинности для регистрации

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>Замените функцию вставки следующим кодом и нажмите кнопку <strong>Сохранить</strong>.</p>
<pre><code>function insert(item, user, request) {
	// Define a payload for the Windows Phone toast notification.
	var payload = '&lt;?xml version="1.0" encoding="utf-8"?>' +
		'&lt;wp:Notification xmlns:wp="WPNotification">&lt;wp:Toast>' +
		'&lt;wp:Text1>New Item&lt;/wp:Text1>&lt;wp:Text2>' + item.text + 
		'&lt;/wp:Text2>&lt;/wp:Toast>&lt;/wp:Notification>';

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

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](../../includes/mobile-services-windows-phone-push-notifications-app-users.md)]


##<a name="test"></a>Тестирование приложения

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Добавление проверки подлинности в приложение]: mobile-services-windows-phone-get-started-users.md
[Добавление push-уведомлений в приложение]: mobile-services-javascript-backend-windows-phone-get-started-push.md
[Добавление push-уведомлений в приложение мобильных служб]: mobile-services-javascript-backend-windows-phone-get-started-push.md
[Azure Management Portal]: https://manage.windowsazure.com/

 

<!---HONumber=July15_HO4-->
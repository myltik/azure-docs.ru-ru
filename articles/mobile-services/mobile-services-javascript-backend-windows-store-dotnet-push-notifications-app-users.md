<properties 
	pageTitle="Отправка push-уведомлений пользователям, прошедшим проверку универсального приложения Windows." 
	description="Узнайте, как отправлять push-уведомления из мобильных служб Azure определенным пользователям универсального приложения Windows C#." 
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
	ms.date="11/02/2015" 
	ms.author="glenga"/>

# Рассылка push-уведомлений проверенным пользователям

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Обзор
В этом разделе показано, как передавать push-уведомления пользователю, прошедшему проверку подлинности, на любом зарегистрированном устройстве. В отличие от предыдущего учебника [Добавление push-уведомлений в приложение мобильных служб], в данном учебнике будут описаны изменения мобильной службы, обеспечивающие выполнение проверки подлинности пользователя перед регистрацией клиента в концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется — добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется серверный скрипт, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.

В этом учебнике выполняются следующие действия.

1. [Обновление службы с учетом требования по проверке подлинности для регистрации]
2. [Обновление приложения с учетом требования по входу в систему перед регистрацией]
3. [Тестирование приложения]
 
Этот учебник поддерживает приложения Магазина Windows и приложения Магазина Windows Phone.

##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Добавление проверки подлинности в приложение]<br/>Добавляет требование входа в демонстрационное приложение TodoList.

+ [Добавление push-уведомлений в приложение]<br/>Настраивает демонстрационное приложение TodoList для push-уведомлений с использованием центров уведомлений.

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

##<a name="register"></a>Обновление службы с учетом требования по проверке подлинности для регистрации

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

&nbsp;&nbsp;5. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**.

	function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
    '<binding template="ToastText01"><text id="1">' +
    item.text + '</text></binding></visual></toast>';

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
	}

&nbsp;&nbsp;Этот скрипт вставки использует тег идентификатора пользователя для отправки push-уведомления (с текстом вставленного элемента) во все регистрации приложения Магазина Windows, созданные выполнившим вход пользователем.

##<a name="update-app"></a>Обновление приложения, чтобы для регистрации требовался вход

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>Тестирование приложения

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Обновление службы с учетом требования по проверке подлинности для регистрации]: #register
[Обновление приложения с учетом требования по входу в систему перед регистрацией]: #update-app
[Тестирование приложения]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Добавление проверки подлинности в приложение]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Добавление push-уведомлений в приложение]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Добавление push-уведомлений в приложение мобильных служб]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=Nov15_HO2-->
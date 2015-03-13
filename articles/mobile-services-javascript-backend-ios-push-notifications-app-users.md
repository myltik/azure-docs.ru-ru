<properties 
	pageTitle="Рассылка push-уведомлений проверенным пользователям" 
	description="Узнайте, как отправлять push-уведомления для конкретного адресата" 
	services="mobile-services, notification-hubs" 
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
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Рассылка push-уведомлений проверенным пользователям

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

В этом разделе показано, как передавать push-уведомления пользователю, прошедшему проверку подлинности, на любом зарегистрированном устройстве iOS. В отличие от предыдущего учебника по [push-уведомлениям][Приступая к работе с push-уведомлениями], в данном учебнике внесены изменения в мобильную службу, чтобы она принуждала пользователя пройти проверку подлинности перед регистрацией клиента iOS на концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется - добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется серверный скрипт, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.

В этом учебнике выполняются следующие действия.

+ [Обновление службы, чтобы для регистрации требовалась проверка подлинности]
+ [Обновление приложения для выполнения входа перед регистрацией]
+ [Тестирование приложения]

##Предварительные требования

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Приступая к работе с проверкой подлинности]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Приступая к работе с push-уведомлениями]<br/>В этом учебнике выполняется настройка демонстрационного приложения TodoList для push-уведомлений с использованием концентратора уведомлений.

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

##<a name="register"></a>Обновление службы, чтобы для регистрации требовалась проверка подлинности

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>Замените функцию вставки следующим кодом и нажмите кнопку <strong>Сохранить</strong>.</p>
<pre><code>function insert(item, user, request) {

        function insert(item, user, request) {
            request.execute();
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

}</code></pre>

<p>Этот скрипт вставки использует тег идентификатора пользователя для отправки push-уведомления (с текстом вставленного элемента) во все регистрации приложения Windows Phone (MPNS), созданные выполнившим вход пользователем.</p></li></ol>


##<a name="update-app"></a>Обновление приложения для выполнения входа перед регистрацией

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Тестирование приложения

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[Обновление службы, чтобы для регистрации требовалась проверка подлинности]: #register
[Обновление приложения для выполнения входа перед регистрацией]: #update-app
[Тестирование приложения]: #test
[Дальнейшие действия]:#next-steps


<!-- URLs. -->
[Приступая к работе с проверкой подлинности]: /ru-ru/documentation/articles/mobile-services-ios-get-started-users/
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник по принципам использования мобильных служб .NET]: /ru-ru/develop/mobile/how-to-guides/work-with-net-client-library

[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png


<!--HONumber=42-->

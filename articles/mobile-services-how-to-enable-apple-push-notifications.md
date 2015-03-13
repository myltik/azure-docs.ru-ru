<properties 
	pageTitle="Как включить push-уведомления Apple" 
	description="Следуйте инструкциям этого учебника, чтобы создать новую службу с помощью мобильных служб Azure." 
	services="mobile-services, notification-hubs" 
	documentationCenter="ios" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Как включить push-уведомления Apple

В этом разделе показано, как включить ваше приложение iOS для push-уведомлений с помощью службы push-уведомлений Apple (APNS). Полученный сертификат используется в целях регистрации приложения IOS для push-уведомлений на [портале управления Azure][Портал управления]. Полный набор учебных курсов, который включает в себя обновления для вашего приложения, см. в разделе [Приступая к работе с push-уведомлениями]. 

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

Теперь этот сертификат .p12 можно использовать, чтобы службы могли проверять подлинность с помощью APNS и отправлять push-уведомления от имени вашего приложения.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
[Пакет SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Портал управления]: https://manage.windowsazure.com/



<!--HONumber=42-->

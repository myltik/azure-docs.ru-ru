<properties 
	pageTitle="Как включить службу Google Cloud Messaging" 
	description="Следуйте инструкциям этого учебника, чтобы создать новую службу с помощью мобильных служб Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Как включить службу Google Cloud Messaging

В этом разделе показано, как включить ваше приложение Android для push-уведомлений с помощью службы Google Cloud Messaging (GCM). Полученный ключ API используется в целях регистрации приложения Android для push-уведомлений на [портале управления Azure][Портал управления]. Полный набор учебных курсов, который включает в себя обновления для вашего приложения, см. в разделе [Приступая к работе с push-уведомлениями]. 

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Теперь значение этого ключа API можно использовать, чтобы службы могли проверять подлинность с помощью службы GCM и отправлять push-уведомления от имени вашего приложения.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Приступая к работе с push-уведомлениями]: /documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Visual Studio 2012 Express для Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Пакет SDK для мобильных служб]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Портал управления]: https://manage.windowsazure.com/
[Серверная версия .NET]: /documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started

<!--HONumber=47-->

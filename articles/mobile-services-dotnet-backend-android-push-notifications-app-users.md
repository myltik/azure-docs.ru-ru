<properties 
	pageTitle="Рассылка push-уведомлений проверенным пользователям" 
	description="Узнайте, как отправлять push-уведомления для конкретного адресата" 
	services="mobile-services,notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Рассылка push-уведомлений проверенным пользователям

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

##Обзор

В этом разделе показано, как передавать push-уведомления пользователю, прошедшему проверку подлинности, на любом зарегистрированном устройстве. В отличие от предыдущего учебника по [push-уведомлениям][Приступая к работе с push-уведомлениями], в данном учебнике внесены изменения в мобильную службу, чтобы она принуждала пользователя пройти проверку подлинности перед регистрацией клиента на концентраторе уведомлений для получения push-уведомлений. Регистрация также изменяется - добавляется тег на основе идентификатора назначенного пользователя. Наконец, обновляется серверный код, чтобы уведомление отправлялось не всем зарегистрированным пользователям, а только тем, кто прошел проверку подлинности.


Этот учебник поддерживает приложения Android.

##Предварительные требования 

Перед началом работы с этим учебником необходимо изучить следующие учебники по мобильным службам.

+ [Добавление проверки подлинности в приложение мобильных служб]<br/>В этом учебнике в демонстрационное приложение TodoList добавляется требование входа.

+ [Приступая к работе с push-уведомлениями]<br/>В этом учебнике выполняется настройка демонстрационного приложения TodoList для push-уведомлений с использованием концентратора уведомлений. 

После выполнения обоих учебников можно приступить к запрету регистрации непроверенных пользователей для получения push-уведомлений от мобильной службы.

##Обновление службы с учетом требования по проверке подлинности для регистрации

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)] 

##Обновление приложения с учетом требования по входу в систему перед регистрацией

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)] 

##Тестирование приложения

[AZURE.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)] 


<!---##Next steps

In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services. Learn more about how to use Mobile Services with .NET in [Mobile Services .NET How-to Conceptual Reference]-->


<!-- URLs. -->
[Добавление проверки подлинности в приложение мобильных служб]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Приступая к работе с push-уведомлениями]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Портал управления Azure]: https://manage.windowsazure.com/
[Справочник по принципам использования мобильных служб .NET]: /develop/mobile/how-to-guides/work-with-net-client-library

<!--HONumber=47-->
